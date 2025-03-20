In Ansible, YAML files use structured data formats to define tasks, variables, inventories, and playbooks. Here’s an introduction to YAML data types in Ansible Playbooks.

---

## 📌 **YAML Data Types in Ansible Playbooks**
YAML supports different data types that are commonly used in playbooks:

### **1️⃣ Strings**
Used for names, paths, commands, and messages.

```yaml
name: "Install Apache"  # Quoted string
package_name: httpd      # Unquoted string
```

### **2️⃣ Numbers (Integers & Floats)**
Used for counting, port numbers, or other numerical values.

```yaml
max_connections: 100
timeout: 30.5  # Float
```

### **3️⃣ Booleans (true/false)**
Used for conditions, enabling/disabling features.

```yaml
enabled: true
use_ssl: false
```

### **4️⃣ Lists (Arrays)**
Used to define multiple values in a sequence.

```yaml
packages:
  - nginx
  - mysql
  - php
```

Or in a single line:

```yaml
packages: [nginx, mysql, php]
```

### **5️⃣ Dictionaries (Key-Value Pairs)**
Used for structured data like configurations.

```yaml
database:
  name: mydb
  user: admin
  password: secret
```

### **6️⃣ Null Values**
Represented as `null`, `~`, or left empty.

```yaml
config: null
path: ~
```

---

## 🎯 **Example Playbook with Different Data Types**
```yaml
---
- name: Configure Web Server
  hosts: web
  become: yes

  vars:
    app_name: "MyApp"          # String
    max_clients: 200           # Integer
    enable_ssl: true           # Boolean
    packages:                  # List
      - nginx
      - php
      - mysql-server
    database:                  # Dictionary
      name: "mydb"
      user: "dbadmin"
      password: "securepass"

  tasks:
    - name: Install required packages
      yum:
        name: "{{ packages }}"
        state: present

    - name: Configure database
      debug:
        msg: "Database {{ database.name }} configured for user {{ database.user }}"
```

---