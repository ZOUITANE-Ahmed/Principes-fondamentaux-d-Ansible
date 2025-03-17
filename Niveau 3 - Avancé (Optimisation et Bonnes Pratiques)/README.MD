# **📌 Niveau 3 : Avancé (Optimisation et Bonnes Pratiques Ansible)**  

Dans ce niveau, nous allons explorer **les stratégies d’optimisation**, **les bonnes pratiques** et la gestion **d’environnements complexes** avec Ansible.  

---

## **1️⃣ Optimisation des Performances Ansible**  

Lorsque l’environnement s’agrandit, Ansible peut devenir **plus lent**. Voici des techniques pour l’optimiser.

### **A. Exécution Parallèle avec Forks**  
📌 **Augmenter le nombre d’hôtes traités simultanément**  

Dans `ansible.cfg`, augmente la valeur de `forks` :  
```ini
[defaults]
forks = 50
```
✅ **Plus de tâches exécutées en parallèle**, surtout utile dans de grands environnements.  

---

### **B. Accélérer SSH avec Pipelining**  
📌 **Éviter l’ouverture répétée de connexions SSH**  

Dans `ansible.cfg`, active `pipelining` :  
```ini
[ssh_connection]
pipelining = True
```
✅ **Moins de latence entre les commandes**.  

---

### **C. Utiliser Fact Caching**  
📌 **Éviter de récupérer les faits (`facts`) à chaque exécution**  

Active le cache des facts pour accélérer l’exécution des playbooks :  
```ini
[defaults]
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_facts
```
✅ **Réduit le temps d’exécution en évitant des requêtes inutiles**.  

---

### **D. Limiter le Nombre de Tâches Exécutées**  
📌 **Utiliser `--limit` et `--tags` pour exécuter uniquement ce qui est nécessaire**  

```bash
ansible-playbook site.yml --limit webservers
ansible-playbook site.yml --tags "install"
```
✅ **Exécute uniquement les tâches ciblées**, évitant des exécutions inutiles.  

---

## **2️⃣ Bonnes Pratiques de Structuration**  

Un projet Ansible bien structuré facilite **la maintenance et la réutilisation**.  

### **A. Organisation d’un Projet Modulaire avec les Rôles**  
📌 **Structure recommandée**  
```
ansible_project/
├── inventories/
│   ├── production
│   ├── staging
│   └── development
├── group_vars/
├── host_vars/
├── roles/
│   ├── webserver/
│   ├── database/
│   ├── application/
│   └── security/
├── playbooks/
│   ├── site.yml
│   ├── webservers.yml
│   ├── databases.yml
│   └── security.yml
└── ansible.cfg
```
✅ **Séparation claire des responsabilités** et **réutilisation des rôles**.  

---

### **B. Utiliser des Variables Structurées et Sécurisées**  
📌 **Centraliser les variables dans `group_vars/` et `host_vars/`**  

Exemple `group_vars/webservers.yml` :  
```yaml
nginx_version: "1.20"
web_root: "/var/www/html"
```
✅ **Évite la duplication des variables dans les playbooks**.  

---

### **C. Sécuriser les Données Sensibles avec Ansible Vault**  
📌 **Chiffrer les fichiers contenant des mots de passe**  
```bash
ansible-vault encrypt group_vars/database.yml
```
Pour **éditer** un fichier chiffré :  
```bash
ansible-vault edit group_vars/database.yml
```
✅ **Protège les informations sensibles** (mots de passe, clés API…).  

---

## **3️⃣ Gestion des Logs et Débogage Avancé**  

📌 **Exécuter un playbook avec un mode détaillé (`-vvvv`)**  
```bash
ansible-playbook playbook.yml -vvvv
```
📌 **Stocker les logs dans un fichier (`ansible.cfg`)**  
```ini
[defaults]
log_path = /var/log/ansible.log
```
✅ **Facilite l’audit et le débogage des exécutions.**  

---

## **4️⃣ Gestion des Environnements Complexes**  

### **A. Déploiement Multi-Tiers (Web, App, DB)**  
📌 **Exemple d’inventaire pour un environnement avec plusieurs couches**  
```yaml
all:
  children:
    webservers:
      hosts:
        web01:
          ansible_host: 192.168.1.10
        web02:
          ansible_host: 192.168.1.11
    appservers:
      hosts:
        app01:
          ansible_host: 192.168.1.20
    dbservers:
      hosts:
        db01:
          ansible_host: 192.168.1.30
```
✅ **Permet de séparer les rôles et d’appliquer des configurations spécifiques**.  

---

### **B. Load Balancing avec HAProxy**  
📌 **Exemple de configuration HAProxy pour répartir la charge entre `web01` et `web02`**  
```yaml
- name: Installer HAProxy
  hosts: lbservers
  become: true
  tasks:
    - name: Installer HAProxy
      apt:
        name: haproxy
        state: present

    - name: Configurer HAProxy
      template:
        src: haproxy.cfg.j2
        dest: /etc/haproxy/haproxy.cfg
      notify: Redémarrer HAProxy

  handlers:
    - name: Redémarrer HAProxy
      service:
        name: haproxy
        state: restarted
```
📌 **Fichier `templates/haproxy.cfg.j2`**  
```ini
frontend http_front
    bind *:80
    default_backend webservers

backend webservers
    balance roundrobin
    server web01 192.168.1.10:80 check
    server web02 192.168.1.11:80 check
```
✅ **HAProxy distribue le trafic HTTP entre plusieurs serveurs**.  

---

## **5️⃣ Automatisation Avancée avec AWX / Ansible Tower**  
AWX (version open-source de **Ansible Tower**) permet de gérer les playbooks via une interface web.  

### **A. Avantages d’AWX**  
✅ Interface graphique pour gérer les playbooks  
✅ Exécutions planifiées  
✅ Logs centralisés et gestion des erreurs  
✅ Contrôle des accès et permissions  

### **B. Installation d’AWX**  
📌 **Installer AWX avec Docker**  
```bash
git clone https://github.com/ansible/awx.git
cd awx/installer
ansible-playbook -i inventory install.yml
```
✅ Une fois installé, accéder à **http://localhost:80** et commencer à exécuter des playbooks.  

---

## **🎯 Objectifs pour passer au Niveau 4 (Expert)**  
🔹 Maîtriser **Ansible Tower/AWX**  
🔹 Automatiser **les infrastructures cloud (AWS, Azure, GCP)**  
🔹 Sécuriser **les déploiements (RBAC, Vault avancé, secrets management)**  
🔹 Optimiser **les performances avec des stratégies avancées**  
🔹 Intégrer **Ansible avec Terraform pour l’Infrastructure as Code**  
