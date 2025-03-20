To configure **Active Directory (AD)** using **Ansible** on a **Windows Server**, you can leverage the `win_feature` and other Windows-specific modules that Ansible provides. Below are the steps to install and configure Active Directory Domain Services (AD DS), create a domain, and configure a Windows Server as a Domain Controller.

### Prerequisites
1. **Windows Server**: Ensure that your Windows Server machine is running a supported version (e.g., Windows Server 2016/2019/2022).
2. **WinRM Setup**: Ensure WinRM is enabled on the target server (as outlined in the previous answer).
3. **Ansible Installed**: Ensure you have Ansible installed on a Linux machine or a control node, with `pywinrm` installed to communicate with Windows.

### 1. **Install Active Directory Domain Services (AD DS) Role**
Before configuring AD, you need to install the **AD DS role** on your server.

Create an Ansible playbook (`install_ad.yml`) to install the AD DS feature:

```yaml
---
- name: Install Active Directory Domain Services
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Install AD DS Role
      win_feature:
        name: AD-Domain-Services
        state: present
```

This will install the **AD DS** feature on your Windows Server. You can execute this playbook using:

```bash
ansible-playbook -i inventory.ini install_ad.yml
```

### 2. **Promote the Server to Domain Controller**
After the AD DS role is installed, the next step is to promote the server to a Domain Controller and create a new domain.

Add tasks in your playbook to configure and promote the server to a Domain Controller.

```yaml
---
- name: Configure Active Directory Domain Services
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Install AD DS Role
      win_feature:
        name: AD-Domain-Services
        state: present

    - name: Promote to Domain Controller
      win_domain_controller:
        name: "example.local"  # Specify your domain name
        safe_mode_password: "P@ssw0rd!"  # Specify the Directory Services Restore Mode (DSRM) password
        domain_admin_user: "Administrator"  # Domain admin username
        domain_admin_password: "YourPassword"  # Domain admin password
        dns_name: "example.local"  # DNS domain name
        state: domain  # 'domain' to create a new domain
        reboot: yes  # Reboot server after promotion
```

In this example:

- `name`: Specifies the fully qualified domain name (FQDN) of the domain (e.g., `example.local`).
- `safe_mode_password`: The password used to recover Active Directory if needed.
- `domain_admin_user` and `domain_admin_password`: The domain admin credentials.
- `dns_name`: The DNS domain name for your AD domain.
- `state`: Set to `domain` to promote the server to a domain controller.
- `reboot`: Set to `yes` to reboot the server after the promotion.

Run the playbook to promote the server:

```bash
ansible-playbook -i inventory.ini configure_ad.yml
```

### 3. **Verify Domain Controller Installation**
After the playbook finishes running, you can verify that the server is now a Domain Controller by logging into the server and using the following command:

```powershell
Get-ADDomainController
```

You should see your domain listed as a Domain Controller.

### 4. **Managing AD Users and Groups**
Once the server is promoted to a Domain Controller, you can use Ansible to manage AD users and groups. Here’s an example of how to create a user in Active Directory.

```yaml
---
- name: Manage Active Directory Users and Groups
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Create a new AD user
      win_user:
        name: "john.doe"
        password: "P@ssw0rd!"
        state: present
        groups: "Domain Users"
        comment: "John Doe, IT Support"
```

### 5. **Configure Group Policies (Optional)**
You can also apply Group Policies (GPOs) to your domain or specific Organizational Units (OUs). Here’s an example of using the `win_gpo` module to configure a GPO:

```yaml
---
- name: Configure Group Policies
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Create a new GPO
      win_gpo:
        name: "Password Policy"
        state: present
        enable: yes
        settings:
          MaximumPasswordAge: 90
          MinimumPasswordLength: 8
```

### 6. **Join Additional Servers to the Domain**
You can also automate the process of joining additional servers to the domain by using the `win_domain_membership` module. Here’s an example:

```yaml
---
- name: Join Server to Domain
  hosts: new_windows_server
  gather_facts: yes
  tasks:
    - name: Join the server to the domain
      win_domain_membership:
        name: "example.local"
        domain_admin_user: "Administrator"
        domain_admin_password: "YourPassword"
        state: domain
```

### 7. **Test Active Directory Configuration**
To verify that everything is working correctly, you can use Ansible to ping the domain controller:

```bash
ansible windows -m win_ping
```

### Additional Modules
Here are some additional Ansible modules for working with Active Directory on Windows servers:

- **win_domain**: Manage domain configurations.
- **win_user**: Create and manage Active Directory users.
- **win_group**: Create and manage AD groups.
- **win_gpo**: Manage Group Policy Objects.
- **win_domain_trust**: Manage domain trusts.
- **win_dhcp_server**: Configure DHCP servers in an AD environment.

### Conclusion
With Ansible, you can automate the installation, configuration, and management of Active Directory Domain Services on Windows Servers. Once your domain is set up, you can continue to use Ansible for ongoing management of users, groups, and GPOs.