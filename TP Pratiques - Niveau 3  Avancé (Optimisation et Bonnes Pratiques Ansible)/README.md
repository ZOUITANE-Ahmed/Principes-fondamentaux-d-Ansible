### **TP - Niveau 3 : Avancé (Optimisation et Bonnes Pratiques Ansible)**  

🎯 **Objectif :** Optimiser des playbooks Ansible avec des boucles, conditions, gestion des erreurs et chiffrement avec **Ansible Vault**.

---

## **🔹 Scénario**  
Tu es administrateur système et tu dois configurer plusieurs serveurs Linux avec :  
✔️ **Création d’utilisateurs** avec des mots de passe sécurisés 🔐  
✔️ **Installation et configuration d’un service** (Apache)  
✔️ **Gestion des erreurs** pour éviter les interruptions  
✔️ **Utilisation de Ansible Vault** pour protéger des secrets  

---

## **🔹 Étape 1 : Préparer l’environnement**  

💡 **Prérequis :**  
- Un serveur Ansible installé (machine de contrôle)  
- Un ou plusieurs nœuds gérés accessibles en SSH  

👉 **Créer un inventaire** `/etc/ansible/inventory` :  
```ini
[webservers]
web1 ansible_host=192.168.56.101 ansible_user=root
web2 ansible_host=192.168.56.102 ansible_user=root
```

🔎 **Tester la connexion :**  
```bash
ansible all -m ping -i /etc/ansible/inventory
```

---

## **🔹 Étape 2 : Créer le playbook principal**  

Créer un fichier `setup_web.yml` :  

```yaml
---
- name: "Configuration des serveurs Web"
  hosts: webservers
  become: yes
  vars_files:
    - secrets.yml  # 🔐 Contient les mots de passe chiffrés
  
  tasks:
    - name: "Créer des utilisateurs"
      user:
        name: "{{ item.name }}"
        password: "{{ item.password | password_hash('sha512') }}"
        state: present
      loop:
        - { name: "admin1", password: "{{ admin1_pass }}" }
        - { name: "admin2", password: "{{ admin2_pass }}" }

    - name: "Installer Apache"
      package:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"

    - name: "Démarrer et activer Apache"
      service:
        name: apache2
        state: started
        enabled: yes
      ignore_errors: yes  # Évite l'interruption en cas d'échec

    - name: "Déployer une page d'accueil"
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
      notify: "Redémarrer Apache"

  handlers:
    - name: "Redémarrer Apache"
      service:
        name: apache2
        state: restarted
```

---

## **🔹 Étape 3 : Sécuriser les mots de passe avec Ansible Vault**  

Créer un fichier `secrets.yml` :  
```yaml
admin1_pass: "MotDePasseAdmin1"
admin2_pass: "MotDePasseAdmin2"
```

🔐 **Chiffrer le fichier** :  
```bash
ansible-vault encrypt secrets.yml
```
👉 Il faudra fournir un mot de passe pour le déchiffrer.  

Exécuter le playbook :  
```bash
ansible-playbook setup_web.yml --ask-vault-pass
```

---

## **🔹 Étape 4 : Vérification et Optimisation**  

1️⃣ Tester les tags :  
```bash
ansible-playbook setup_web.yml --tags "apache"
```

2️⃣ Ajouter des conditions (`when`), par exemple pour appliquer un réglage spécifique à Debian ou RedHat.  

3️⃣ Optimiser l'exécution avec `async` et `poll` pour des tâches longues.  
