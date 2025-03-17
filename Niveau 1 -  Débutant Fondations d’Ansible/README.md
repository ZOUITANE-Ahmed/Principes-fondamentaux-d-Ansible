### **📌 Niveau 1 : Débutant (Fondations d’Ansible)**  

Ce niveau est destiné à ceux qui découvrent **Ansible** et souhaitent comprendre ses principes fondamentaux. Il couvre l'installation, la configuration et l'exécution de commandes simples.

---

## **1️⃣ Introduction à Ansible : Pourquoi l'utiliser ?**  
Ansible est un outil d’automatisation **sans agent** utilisé pour :  
✅ Gérer les configurations des serveurs  
✅ Automatiser l'installation de logiciels  
✅ Déployer des applications  
✅ Orchestrer des tâches sur plusieurs machines  

**Pourquoi choisir Ansible ?**  
✔ Facile à apprendre (utilise YAML)  
✔ Agentless (pas besoin d'installer un agent sur les machines cibles)  
✔ Basé sur SSH (sécurisé et simple)  
✔ Extensible et utilisé dans les grandes entreprises  

---

## **2️⃣ Architecture d'Ansible**  
Ansible fonctionne avec un **nœud de contrôle** (où Ansible est installé) et plusieurs **nœuds gérés** (serveurs à automatiser).  

### **📌 Composants clés :**  
- **Control Node** 🖥 : La machine où Ansible est installé et exécuté  
- **Managed Nodes** 🖧 : Les serveurs à automatiser  
- **Inventory** 📋 : Fichier contenant la liste des serveurs  
- **Modules** 📦 : Commandes Ansible préconstruites pour exécuter des tâches  
- **Playbooks** 📜 : Fichiers YAML contenant des instructions pour Ansible  

---

## **3️⃣ Installation d’Ansible**  

### **🔹 Sur Red Hat / CentOS :**  
```bash
sudo yum install -y ansible
```

### **🔹 Sur Debian / Ubuntu :**  
```bash
sudo apt update && sudo apt install -y ansible
```

### **🔹 Vérification de l’installation :**  
```bash
ansible --version
```

---

## **4️⃣ Configuration de l’inventaire**  
L'inventaire définit les **machines** qu'Ansible doit gérer. Par défaut, il est stocké dans `/etc/ansible/hosts`, mais vous pouvez créer votre propre fichier d’inventaire.

### **Exemple de fichier d’inventaire (`inventory.ini`) :**
```ini
[webservers]
web1 ansible_host=192.168.1.10 ansible_user=root
web2 ansible_host=192.168.1.11 ansible_user=root

[databases]
db1 ansible_host=192.168.1.20 ansible_user=root
```

📌 **Groupes** : Les serveurs sont regroupés (`[webservers]`, `[databases]`) pour faciliter leur gestion.

---

## **5️⃣ Vérification de la connexion avec SSH**  
Assurez-vous que vous pouvez vous connecter aux serveurs distants sans mot de passe via SSH.  

### **Générer une clé SSH (si ce n'est pas déjà fait) :**  
```bash
ssh-keygen -t rsa -b 4096
```

### **Copier la clé publique sur les nœuds distants :**  
```bash
ssh-copy-id root@192.168.1.10
```

### **Tester la connexion avec Ansible :**  
```bash
ansible all -m ping -i inventory.ini
```
📌 **Réponse attendue :**  
```json
web1 | SUCCESS => {
    "ping": "pong"
}
web2 | SUCCESS => {
    "ping": "pong"
}
```

---

## **6️⃣ Exécution de commandes Ad-Hoc**  
Les commandes **ad-hoc** permettent d'exécuter une tâche rapidement sans écrire de playbook.

### **Exemple : Vérifier l’espace disque sur tous les serveurs**  
```bash
ansible all -m command -a "df -h" -i inventory.ini
```

### **Autres commandes utiles :**
| Commande | Description |
|----------|------------|
| `ansible all -m ping` | Vérifie la connectivité des nœuds |
| `ansible all -m command -a "uptime"` | Vérifie le temps d'exécution des machines |
| `ansible all -m service -a "name=nginx state=started"` | Démarre le service **nginx** |

---

## **7️⃣ Premiers Playbooks Ansible**  
Un **playbook** est un fichier YAML contenant plusieurs tâches d'automatisation.

📌 **Exemple : Installer Apache sur des serveurs web**
```yaml
- name: Installer Apache
  hosts: webservers
  become: true
  tasks:
    - name: Installer le paquet Apache
      apt:
        name: apache2
        state: present
```

📌 **Exécution du playbook :**  
```bash
ansible-playbook -i inventory.ini install_apache.yml
```

---

## **🎯 Objectifs pour passer au niveau suivant :**  
✅ Comprendre l’architecture d’Ansible  
✅ Installer et configurer Ansible sur une machine de contrôle  
✅ Gérer un fichier d’inventaire et tester la connectivité des nœuds  
✅ Exécuter des commandes ad-hoc sur des serveurs distants  
✅ Écrire et exécuter un premier playbook simple  
