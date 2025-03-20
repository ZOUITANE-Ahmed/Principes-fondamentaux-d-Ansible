Pour créer des utilisateurs sur **Active Directory** (AD) à l'aide d'Ansible, vous pouvez utiliser le module **`win_user`**, qui permet de gérer les utilisateurs sur un serveur Windows, y compris dans un environnement Active Directory. Voici un guide étape par étape pour créer des utilisateurs sur Active Directory avec Ansible.

### Prérequis

1. **Serveur Windows configuré comme contrôleur de domaine** avec Active Directory installé et configuré.
2. **Ansible installé** sur un nœud de contrôle (généralement une machine Linux).
3. **Connexion WinRM fonctionnelle** entre le nœud de contrôle et le serveur Windows (comme décrit dans les étapes précédentes).
4. **Droits d'administrateur sur le domaine** pour pouvoir créer des utilisateurs.

### Étapes pour créer un utilisateur Active Directory avec Ansible

### 1. **Créer un Playbook Ansible pour ajouter des utilisateurs**

Voici un exemple de playbook Ansible (`create_ad_user.yml`) pour ajouter un ou plusieurs utilisateurs dans Active Directory :

```yaml
---
- name: Créer des utilisateurs dans Active Directory
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Créer un utilisateur AD
      win_user:
        name: "john.doe"  # Nom d'utilisateur dans Active Directory
        password: "P@ssw0rd!"  # Mot de passe de l'utilisateur
        state: present  # Utilisateur à créer ou à modifier
        groups: "Domain Users"  # Groupe(s) auquel/auxquels l'utilisateur appartient
        comment: "John Doe, IT Support"  # Commentaire pour l'utilisateur
        fullname: "John Doe"  # Nom complet de l'utilisateur
        home_directory: "C:\\Users\\john.doe"  # Dossier personnel
        profile_path: "C:\\Users\\john.doe\\profile"  # Profil de l'utilisateur
        login_script: "logon.bat"  # Script de connexion (optionnel)
        domain: "example.local"  # Nom du domaine AD (si nécessaire)

    - name: Créer un autre utilisateur AD
      win_user:
        name: "jane.doe"
        password: "P@ssw0rd!"
        state: present
        groups: "Domain Users"
        comment: "Jane Doe, HR Department"
        fullname: "Jane Doe"
        home_directory: "C:\\Users\\jane.doe"
        profile_path: "C:\\Users\\jane.doe\\profile"
        login_script: "logon.bat"
        domain: "example.local"
```

### Explication des principales options utilisées :

- **`name`** : Nom de l'utilisateur dans Active Directory (ex : `john.doe`).
- **`password`** : Mot de passe de l'utilisateur. Il doit répondre aux politiques de sécurité du domaine.
- **`state`** : Si `present`, Ansible crée l'utilisateur. Si `absent`, il supprime l'utilisateur.
- **`groups`** : Spécifie les groupes AD auxquels l'utilisateur appartient (par exemple, `Domain Users`).
- **`comment`** : Un commentaire décrivant l'utilisateur.
- **`fullname`** : Le nom complet de l'utilisateur.
- **`home_directory`** et **`profile_path`** : Dossiers personnels et profils pour l'utilisateur.
- **`login_script`** : Script de connexion à exécuter lors de la connexion de l'utilisateur (optionnel).
- **`domain`** : Le domaine Active Directory, utilisé si nécessaire.

### 2. **Exécuter le Playbook**

Une fois le playbook créé, vous pouvez l'exécuter avec la commande suivante :

```bash
ansible-playbook -i inventory.ini create_ad_user.yml
```

Cela va ajouter les utilisateurs définis dans le playbook à Active Directory. Assurez-vous que **`inventory.ini`** contient les informations nécessaires pour la connexion à votre serveur Windows et que WinRM est configuré correctement.

### 3. **Vérifier les utilisateurs créés**

Une fois que le playbook a été exécuté, vous pouvez vérifier que les utilisateurs ont été créés en vous connectant à votre contrôleur de domaine et en utilisant la commande PowerShell suivante pour lister les utilisateurs AD :

```powershell
Get-ADUser -Filter *
```

Cela retournera tous les utilisateurs du domaine, vous permettant de vérifier que **`john.doe`** et **`jane.doe`** ont bien été créés.

### 4. **Ajouter plusieurs utilisateurs (optionnel)**

Si vous souhaitez ajouter plusieurs utilisateurs à la fois, vous pouvez définir une liste d’utilisateurs dans votre playbook, puis itérer sur cette liste :

```yaml
---
- name: Créer des utilisateurs dans Active Directory
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Créer des utilisateurs AD
      win_user:
        name: "{{ item.name }}"
        password: "{{ item.password }}"
        state: present
        groups: "Domain Users"
        comment: "{{ item.comment }}"
        fullname: "{{ item.fullname }}"
        home_directory: "C:\\Users\\{{ item.name }}"
        profile_path: "C:\\Users\\{{ item.name }}\\profile"
        login_script: "logon.bat"
        domain: "example.local"
      loop:
        - { name: "john.doe", password: "P@ssw0rd!", comment: "John Doe, IT Support", fullname: "John Doe" }
        - { name: "jane.doe", password: "P@ssw0rd!", comment: "Jane Doe, HR Department", fullname: "Jane Doe" }
```

### 5. **Suppression des utilisateurs (optionnel)**

Si vous souhaitez supprimer un utilisateur, vous pouvez utiliser **`state: absent`** dans le playbook, comme suit :

```yaml
- name: Supprimer un utilisateur AD
  hosts: windows
  gather_facts: yes
  tasks:
    - name: Supprimer un utilisateur AD
      win_user:
        name: "john.doe"
        state: absent
```

### Conclusion

Avec Ansible, vous pouvez facilement automatiser la gestion des utilisateurs dans **Active Directory**, y compris leur création, modification ou suppression. Cette méthode permet de gagner du temps dans un environnement d'entreprise avec de nombreux utilisateurs.