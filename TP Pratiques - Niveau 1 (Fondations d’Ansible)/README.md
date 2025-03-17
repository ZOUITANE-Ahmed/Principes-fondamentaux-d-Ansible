### **🔹 Exercices Pratiques - Niveau 1 (Fondations d’Ansible) 🔹**  

Ces exercices vous aideront à appliquer les concepts appris et à renforcer votre compréhension d’Ansible.  

---

## **📌 TP 1 : Installation et Vérification d’Ansible**  
### **Objectif** : Installer Ansible et vérifier son bon fonctionnement.  

**✔ Tâches à réaliser :**  
1️⃣ Installez **Ansible** sur votre machine de contrôle.  
2️⃣ Vérifiez l’installation avec la commande :  
   ```bash
   ansible --version
   ```  
3️⃣ Configurez un **fichier d’inventaire** (`inventory.ini`) contenant **au moins 2 serveurs** (réels ou machines virtuelles).  
4️⃣ Testez la connexion SSH avec :  
   ```bash
   ansible all -m ping -i inventory.ini
   ```  

📌 **Attendu** : Réponse **"pong"** pour chaque machine.

---

## **📌 TP 2 : Exécution de Commandes Ad-Hoc**  
### **Objectif** : Exécuter des commandes Ansible sur les machines distantes.  

**✔ Tâches à réaliser :**  
1️⃣ Affichez le **nom d’hôte** des machines distantes :  
   ```bash
   ansible all -m command -a "hostname" -i inventory.ini
   ```  
2️⃣ Vérifiez l’**espace disque** disponible :  
   ```bash
   ansible all -m command -a "df -h" -i inventory.ini
   ```  
3️⃣ Listez les utilisateurs connectés :  
   ```bash
   ansible all -m command -a "who" -i inventory.ini
   ```  
4️⃣ Redémarrez tous les serveurs (optionnel) :  
   ```bash
   ansible all -m command -a "reboot" -i inventory.ini
   ```  

📌 **Attendu** : Réponse affichant les résultats de chaque commande.

---

## **📌 TP 3 : Premier Playbook Ansible**  
### **Objectif** : Créer un playbook pour installer un service sur les serveurs.  

**✔ Tâches à réaliser :**  
1️⃣ **Créez un fichier** `install_nginx.yml`.  
2️⃣ **Écrivez un playbook** qui installe **NGINX** sur tous les serveurs du groupe `[webservers]`.  

📌 **Fichier `install_nginx.yml` :**
```yaml
- name: Installer et démarrer NGINX
  hosts: webservers
  become: true
  tasks:
    - name: Installer le paquet NGINX
      apt:
        name: nginx
        state: present

    - name: Démarrer et activer NGINX
      service:
        name: nginx
        state: started
        enabled: yes
```

3️⃣ Exécutez le playbook avec :  
   ```bash
   ansible-playbook -i inventory.ini install_nginx.yml
   ```

📌 **Attendu** :  
✔ NGINX installé sur tous les serveurs du groupe `[webservers]`.  
✔ NGINX activé et démarré automatiquement au redémarrage des machines.  

---

## **📌 TP 4 : Sécurisation avec Ansible Vault**  
### **Objectif** : Chiffrer un fichier contenant des mots de passe sensibles.  

**✔ Tâches à réaliser :**  
1️⃣ **Créez un fichier de variables sensibles** `secrets.yml` avec :  
   ```yaml
   admin_password: "SuperSecret123!"
   ```  
2️⃣ Chiffrez-le avec Ansible Vault :  
   ```bash
   ansible-vault encrypt secrets.yml
   ```  
3️⃣ Déchiffrez-le temporairement pour le modifier :  
   ```bash
   ansible-vault edit secrets.yml
   ```  
4️⃣ Affichez son contenu (en fournissant le mot de passe) :  
   ```bash
   ansible-vault view secrets.yml
   ```  

📌 **Attendu** : Fichier chiffré et illisible sans le mot de passe.  

---

## **📌 TP 5 : Test Final - Automatisation Complète 🚀**  
### **Objectif** : Automatiser complètement la configuration d’un serveur web.  

**✔ Tâches à réaliser :**  
1️⃣ **Créez un playbook** `setup_webserver.yml` qui :  
   - Installe **NGINX**  
   - Crée une **page d’accueil personnalisée**  
   - Configure le **pare-feu pour autoriser le trafic HTTP**  

📌 **Fichier `setup_webserver.yml` :**  
```yaml
- name: Configuration complète d’un serveur web
  hosts: webservers
  become: true
  tasks:
    - name: Installer NGINX
      apt:
        name: nginx
        state: present

    - name: Démarrer et activer NGINX
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Créer une page d’accueil
      copy:
        dest: /var/www/html/index.html
        content: "<h1>Bienvenue sur mon serveur web !</h1>"

    - name: Ouvrir le port HTTP (80)
      ufw:
        rule: allow
        port: "80"
        proto: tcp
```

2️⃣ Exécutez le playbook :  
   ```bash
   ansible-playbook -i inventory.ini setup_webserver.yml
   ```  
3️⃣ **Testez l’accès au serveur web** en ouvrant l’URL de votre serveur dans un navigateur :  
   ```
   http://<adresse_IP_du_serveur>
   ```

📌 **Attendu** :  
✔ Un serveur web configuré automatiquement et accessible via HTTP.  
✔ Une page d’accueil affichant "Bienvenue sur mon serveur web !".  

---

### **🎯 Résumé des compétences acquises :**  
✅ Installation et configuration d’Ansible  
✅ Exécution de commandes Ad-Hoc  
✅ Écriture et exécution de playbooks  
✅ Sécurisation des fichiers avec Ansible Vault  
✅ Automatisation complète d’un serveur web  

---

**💡 Prêt pour le Niveau 2 ?**  
Le prochain niveau couvrira :  
🔹 **Playbooks avancés** (variables, handlers, loops)  
🔹 **Gestion des rôles et bonnes pratiques**  
🔹 **Automatisation d’environnements plus complexes**  
