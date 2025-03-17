# **📌 Niveau 2 : Intermédiaire (Automatisation et Playbooks Avancés)**  

À ce niveau, nous allons approfondir l'utilisation d'Ansible avec des concepts plus avancés tels que **les variables, les handlers, les boucles, les rôles et les fichiers de configuration avancés**.  

---

## **1️⃣ Variables et Fichiers de Variables**  
Les **variables** permettent de rendre les playbooks plus dynamiques et réutilisables.  

📌 **Exemple : Définir des variables dans un playbook**  
```yaml
- name: Installer un serveur web
  hosts: webservers
  become: true
  vars:
    web_package: nginx
  tasks:
    - name: Installer le package web
      apt:
        name: "{{ web_package }}"
        state: present
```
**Explication :**  
✔ La variable `web_package` est définie et utilisée avec `{{ }}`.  
✔ Si vous voulez changer le serveur web, vous pouvez remplacer `nginx` par `apache2` sans modifier le playbook.  

---

## **2️⃣ Fichiers de Variables Externes**  
Au lieu de définir les variables directement dans le playbook, vous pouvez utiliser un fichier séparé.  

📌 **Fichier `vars.yml`**  
```yaml
web_package: nginx
```

📌 **Playbook utilisant un fichier de variables**  
```yaml
- name: Installer un serveur web
  hosts: webservers
  become: true
  vars_files:
    - vars.yml
  tasks:
    - name: Installer le package web
      apt:
        name: "{{ web_package }}"
        state: present
```

---

## **3️⃣ Handlers (Gestion des Notifications)**  
Les **handlers** sont des tâches qui ne s'exécutent que si elles sont déclenchées.  

📌 **Exemple : Redémarrer NGINX après une modification**  
```yaml
- name: Installer et configurer NGINX
  hosts: webservers
  become: true
  tasks:
    - name: Installer NGINX
      apt:
        name: nginx
        state: present

    - name: Modifier le fichier de configuration
      copy:
        dest: /etc/nginx/nginx.conf
        content: "worker_processes 4;"
      notify: Redémarrer NGINX

  handlers:
    - name: Redémarrer NGINX
      service:
        name: nginx
        state: restarted
```
**Explication :**  
✔ Le handler **"Redémarrer NGINX"** ne s'exécute que si la tâche **"Modifier le fichier de configuration"** change quelque chose.

---

## **4️⃣ Boucles et Conditions**  
Les **boucles** permettent d'exécuter une tâche plusieurs fois, et les **conditions** permettent d'exécuter des tâches selon des critères spécifiques.  

📌 **Boucle avec `with_items` pour installer plusieurs paquets**  
```yaml
- name: Installer plusieurs paquets
  hosts: all
  become: true
  tasks:
    - name: Installer les packages nécessaires
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - curl
        - git
```

📌 **Condition : Installer Apache uniquement si la distribution est Debian**  
```yaml
- name: Installer Apache si le système est Debian
  hosts: webservers
  become: true
  tasks:
    - name: Installer Apache
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"
```

---

## **5️⃣ Rôles Ansible (Structure Modulaire)**  
Les **rôles** permettent d'organiser les playbooks en modules réutilisables.  

📌 **Création d’un rôle nommé `webserver`**  
```bash
ansible-galaxy init webserver
```
Cela crée la structure suivante :  
```
webserver/
│── tasks/           # Contient les tâches du rôle
│── handlers/        # Contient les handlers du rôle
│── templates/       # Contient les fichiers de configuration dynamiques
│── files/           # Contient les fichiers statiques à copier
│── vars/            # Contient les variables spécifiques au rôle
│── defaults/        # Contient les valeurs par défaut des variables
│── meta/            # Contient des informations sur le rôle
│── README.md        # Documentation du rôle
```

📌 **Fichier `webserver/tasks/main.yml`**  
```yaml
- name: Installer NGINX
  apt:
    name: nginx
    state: present

- name: Démarrer et activer NGINX
  service:
    name: nginx
    state: started
    enabled: yes
```

📌 **Utilisation du rôle dans un playbook**  
```yaml
- name: Déployer un serveur web avec un rôle
  hosts: webservers
  become: true
  roles:
    - webserver
```

**Explication :**  
✔ Le rôle **webserver** est appelé et exécute toutes les tâches définies dans `tasks/main.yml`.  

---

## **6️⃣ Ansible Vault (Gestion des Mots de Passe Sécurisés)**  
Lorsque vous devez stocker des mots de passe ou des clés d’API, utilisez **Ansible Vault**.  

📌 **Chiffrer un fichier contenant un mot de passe**  
```bash
ansible-vault encrypt secrets.yml
```

📌 **Utiliser un fichier chiffré dans un playbook**  
```yaml
- name: Utiliser un mot de passe chiffré
  hosts: all
  become: true
  vars_files:
    - secrets.yml
  tasks:
    - name: Afficher le mot de passe
      debug:
        msg: "Le mot de passe est {{ admin_password }}"
```

---

## **7️⃣ Gestion des Logs et Débogage**  
📌 **Afficher les variables d’un hôte**  
```bash
ansible all -m setup -i inventory.ini
```

📌 **Exécuter un playbook en mode verbeux (-v, -vv, -vvv)**  
```bash
ansible-playbook -i inventory.ini playbook.yml -vv
```

---

## **🎯 Objectifs pour passer au Niveau Avancé :**  
✅ Utiliser **des variables et fichiers de variables**  
✅ Gérer des tâches avec **des handlers**  
✅ Créer des **playbooks modulaires avec des boucles et conditions**  
✅ Organiser ses projets avec **des rôles Ansible**  
✅ Sécuriser ses données avec **Ansible Vault**  
✅ Optimiser le débogage avec **les logs et modes verbeux**  

---

### **🚀 Prêt pour le Niveau 3 ?**  
Le prochain niveau couvrira :  
🔹 **Automatisation d’environnements complexes (HA, Load Balancer, DBs, etc.)**  
🔹 **Gestion avancée avec AWX/Tower**  
🔹 **Optimisation des performances avec Ansible**  
