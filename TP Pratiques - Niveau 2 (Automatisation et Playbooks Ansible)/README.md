Voici un **TP pratique** complet pour le **Niveau 2 : Intermédiaire (Automatisation et Playbooks)** d'Ansible. Ce TP se concentre sur l'automatisation de l'installation d'un serveur web, l'utilisation des modules Ansible pour effectuer des actions, et l'organisation d'un projet avec des rôles.

---

# **🛠️ TP : Automatisation de l'Installation et Configuration d'un Serveur Web avec Ansible**

## **🎯 Objectif :**
- Apprendre à écrire et exécuter des **Playbooks** Ansible pour automatiser la gestion de serveurs.
- Utiliser des **modules Ansible** pour installer des packages, configurer des services et déployer des fichiers.
- Organiser un projet Ansible avec l'utilisation de **rôles** et de **variables**.

---

## **1️⃣ Préparation de l’Environnement**

### **1.1 Créer un fichier d'inventaire**
Tu vas commencer par créer un fichier d'inventaire qui définira les hôtes cibles.

- Crée un fichier nommé `inventory.yml` :
  ```yaml
  all:
    hosts:
      webserver1:
        ansible_host: 192.168.1.10
  ```

- **Tester la connectivité** avec la commande suivante pour vérifier que tu peux accéder à l'hôte :
  ```bash
  ansible all -m ping -i inventory.yml
  ```

---

## **2️⃣ Écriture d’un Playbook pour Installer et Configurer Nginx**

### **2.1 Créer le Playbook de Base pour Installer Nginx**
Tu vas créer un playbook pour installer le serveur web **Nginx** sur ton serveur distant.

- Crée un fichier `install_nginx.yml` :
  ```yaml
  ---
  - name: Installer et configurer Nginx sur le serveur
    hosts: webserver1
    become: yes
    tasks:
      - name: Installer Nginx
        ansible.builtin.apt:
          name: nginx
          state: present

      - name: Démarrer et activer Nginx
        ansible.builtin.service:
          name: nginx
          state: started
          enabled: true
  ```

- **Exécuter le playbook** pour installer et démarrer Nginx sur le serveur distant :
  ```bash
  ansible-playbook -i inventory.yml install_nginx.yml
  ```

- **Vérifier l’installation** en accédant à `http://192.168.1.10` dans un navigateur.

---

## **3️⃣ Utilisation des Variables et Templates Jinja2**

### **3.1 Créer une Variable pour Personnaliser le Site Web**
Les variables permettent de personnaliser facilement des configurations.

- Crée un fichier `vars.yml` :
  ```yaml
  ---
  site_name: "Mon Serveur Web"
  ```

### **3.2 Modifier le Playbook pour Utiliser la Variable**
Modifie ton fichier `install_nginx.yml` pour inclure une variable `site_name` et personnaliser le contenu de la page d’accueil.

- Met à jour le playbook pour qu'il utilise cette variable et crée un fichier HTML personnalisé :
  ```yaml
  ---
  - name: Installer et configurer Nginx sur le serveur
    hosts: webserver1
    become: yes
    vars_files:
      - vars.yml
    tasks:
      - name: Installer Nginx
        ansible.builtin.apt:
          name: nginx
          state: present

      - name: Créer une page d'accueil personnalisée
        ansible.builtin.template:
          src: templates/index.html.j2
          dest: /var/www/html/index.html

      - name: Démarrer et activer Nginx
        ansible.builtin.service:
          name: nginx
          state: started
          enabled: true
  ```

### **3.3 Créer un Template Jinja2 pour la Page d'Accueil**
Les templates Jinja2 permettent de générer des fichiers de manière dynamique.

- Crée un fichier `templates/index.html.j2` avec le contenu suivant :
  ```html
  <!DOCTYPE html>
  <html lang="fr">
  <head>
      <meta charset="UTF-8">
      <title>{{ site_name }}</title>
  </head>
  <body>
      <h1>Bienvenue sur {{ site_name }}</h1>
  </body>
  </html>
  ```

### **3.4 Exécuter à Nouveau le Playbook**
Exécute le playbook pour appliquer les changements et vérifier que le fichier HTML personnalisé a bien été créé :
```bash
ansible-playbook -i inventory.yml install_nginx.yml
```

---

## **4️⃣ Organisation du Projet avec des Rôles**

### **4.1 Créer un Rôle pour Nginx**
Les rôles permettent de mieux organiser ton projet et de le rendre plus modulaire.

- Crée un rôle pour Nginx en utilisant `ansible-galaxy` :
  ```bash
  ansible-galaxy init roles/nginx
  ```

### **4.2 Déplacer les Tâches vers le Rôle**
Déplace les tâches d’installation de Nginx et de gestion de la page d'accueil vers le rôle `nginx`.

- Dans `roles/nginx/tasks/main.yml`, ajoute les tâches suivantes :
  ```yaml
  ---
  - name: Installer Nginx
    ansible.builtin.apt:
      name: nginx
      state: present

  - name: Créer une page d'accueil personnalisée
    ansible.builtin.template:
      src: templates/index.html.j2
      dest: /var/www/html/index.html

  - name: Démarrer et activer Nginx
    ansible.builtin.service:
      name: nginx
      state: started
      enabled: true
  ```

### **4.3 Modifier le Playbook pour Utiliser le Rôle**
Modifie ton fichier `install_nginx.yml` pour inclure le rôle `nginx` au lieu des tâches directes.

- Le playbook modifié :
  ```yaml
  ---
  - name: Installer et configurer Nginx sur le serveur
    hosts: webserver1
    become: yes
    roles:
      - nginx
  ```

### **4.4 Exécuter le Playbook avec le Rôle**
Exécute le playbook une dernière fois pour vérifier que le rôle fonctionne correctement :
```bash
ansible-playbook -i inventory.yml install_nginx.yml
```

---

## **5️⃣ Tester et Vérifier le Déploiement**

1. **Vérifier l'installation de Nginx** en accédant à `http://192.168.1.10` dans le navigateur.  
2. **Vérifier la page d’accueil** générée par le template Jinja2 et confirmée avec la variable `site_name`.

---

## **📌 Résultat attendu**
- Installation et configuration de **Nginx** sur un serveur distant.
- Personnalisation de la page d'accueil via des **variables** et des **templates** Jinja2.
- Organisation du projet avec des **rôles** pour une gestion plus modulaire.

---

🔥 **As-tu des questions sur l’une des étapes ou souhaites-tu aller plus loin avec un autre TP ?** 😃