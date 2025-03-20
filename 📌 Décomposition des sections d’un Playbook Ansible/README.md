# 📌 **Décomposition des sections d’un Playbook Ansible**  

Un **Playbook Ansible** est un script écrit en YAML permettant d’automatiser la configuration et le déploiement des systèmes. Il est composé de plusieurs sections qui définissent **quoi** exécuter et **comment**.

---

## 🔹 **1. En-tête du Playbook (`---`)**
Chaque fichier YAML commence par `---`, ce qui indique le début d’un document YAML.

```yaml
---
```

---

## 🔹 **2. Définition du Play (Niveau supérieur)**
Un **Play** définit un ensemble de **tâches** qui seront exécutées sur un ou plusieurs hôtes cibles.

```yaml
- name: "Configurer le serveur Web"  # Nom descriptif (optionnel)
  hosts: web_servers                 # Groupe d'hôtes ciblé
  become: yes                         # Exécution avec privilèges sudo
```

### ✅ **Directives principales :**
| Directive  | Description |
|------------|------------|
| `name`     | (Optionnel) Nom du Play pour plus de lisibilité. |
| `hosts`    | Définit les machines cibles du Playbook. |
| `become`   | Permet d'exécuter les tâches en tant que super-utilisateur. |
| `gather_facts` | Récupère des informations sur le système cible (`yes` par défaut). |

---

## 🔹 **3. Variables (`vars`)**
Les **variables** stockent des valeurs dynamiques réutilisables.

```yaml
  vars:
    app_name: "MonApplication"
    max_clients: 100
    web_packages:
      - nginx
      - php
      - mysql-server
```

---

## 🔹 **4. Tâches (`tasks`)**
Une liste de **tâches** qui exécutent des actions sur les hôtes cibles. Chaque tâche utilise un **module Ansible**.

```yaml
  tasks:
    - name: "Installer les paquets du serveur Web"
      yum:
        name: "{{ web_packages }}"
        state: present
```

### ✅ **Principaux éléments :**
| Élément  | Description |
|----------|------------|
| `name`   | Nom descriptif de la tâche. |
| `module` | Module Ansible utilisé (ex. `yum`, `apt`, `copy`). |
| `args`   | Arguments du module. |

---

## 🔹 **5. Gestionnaires (`handlers`)**
Les **handlers** sont déclenchés **uniquement lorsqu'ils sont notifiés** par une tâche.

```yaml
  handlers:
    - name: Redémarrer Nginx
      service:
        name: nginx
        state: restarted
```

### ✅ **Utilisation :**
Dans une tâche, utilisez `notify` pour appeler un handler :

```yaml
    - name: "Modifier la configuration de Nginx"
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf
      notify: Redémarrer Nginx
```

---

## 🔹 **6. Rôles (`roles`)**
Les **rôles** permettent d’organiser et réutiliser des tâches.

```yaml
  roles:
    - webserver
    - database
```

L’arborescence d’un rôle ressemble à ceci :

```
roles/
  webserver/
    tasks/
    handlers/
    templates/
    files/
```

---

## 🔹 **7. Modèles (`templates/`)**
Ansible utilise **Jinja2** pour générer des fichiers dynamiquement.

Exemple : `nginx.conf.j2`
```yaml
server {
  listen 80;
  server_name {{ app_name }};
}
```

Utilisation dans une tâche :
```yaml
    - name: "Déployer la configuration Nginx"
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
```

---

## 🔹 **8. Conditions (`when`)**
Permet d'exécuter une tâche **sous certaines conditions**.

```yaml
    - name: "Redémarrer Nginx uniquement sous CentOS"
      service:
        name: nginx
        state: restarted
      when: ansible_distribution == "CentOS"
```

---

## 🔹 **9. Boucles (`loop`)**
Permet d'exécuter une tâche plusieurs fois avec différentes valeurs.

```yaml
    - name: "Installer plusieurs paquets"
      yum:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - php
        - mysql-server
```

---

## 🔹 **10. Débogage (`debug`)**
Affiche des messages ou des valeurs de variables pour le **dépannage**.

```yaml
    - name: "Afficher le nom de l'application"
      debug:
        msg: "Déploiement de {{ app_name }}"
```

---

# 🎯 **Exemple complet de Playbook**
```yaml
---
- name: "Déployer un serveur Web"
  hosts: web_servers
  become: yes

  vars:
    app_name: "MonSiteWeb"
    packages:
      - nginx
      - php
      - mysql-server

  tasks:
    - name: "Installer les paquets requis"
      yum:
        name: "{{ packages }}"
        state: present

    - name: "Déployer la configuration Nginx"
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Redémarrer Nginx

  handlers:
    - name: Redémarrer Nginx
      service:
        name: nginx
        state: restarted
```