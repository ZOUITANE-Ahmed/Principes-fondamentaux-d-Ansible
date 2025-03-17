# **📌 Niveau 4 : Professionnel - Automatisation à l’échelle entreprise**  

## **🔹 1. Déploiement d’Ansible à grande échelle**  
🎯 **Objectif** : Gérer une infrastructure **multi-serveurs** et centraliser l’exécution des tâches Ansible.  

### **1.1 - Gestion centralisée avec Ansible Tower (AWX)**  
✅ **Installation d’AWX sur RHEL**  
```bash
# Installer Docker et dépendances
dnf install -y podman python3-pip git

# Cloner AWX Operator
git clone https://github.com/ansible/awx-operator.git
cd awx-operator

# Déployer AWX Operator
ansible-playbook -i inventory deploy_awx.yml
```
📌 **AWX** permet d’exécuter des **playbooks Ansible** via une **interface web** et d’**automatiser les tâches récurrentes**.  

---

### **1.2 - Intégration avec CI/CD (Jenkins, GitOps)**  
🎯 **Déploiement automatique d’applications après chaque commit**  

✅ **Pipeline Jenkinsfile** pour exécuter Ansible  
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/monrepo.git'
            }
        }
        stage('Deploy') {
            steps {
                sh 'ansible-playbook -i inventory deploy_app.yml'
            }
        }
    }
}
```
🚀 **Résultat** : Chaque commit **déploie automatiquement** une mise à jour via **Jenkins et Ansible**.  

---

### **1.3 - Automatisation via API REST et Webhooks**  
🎯 **Exécuter Ansible depuis une API externe**  

✅ **Utiliser l’API AWX pour lancer un job Ansible**  
```bash
curl -X POST "http://awx.local/api/v2/job_templates/5/launch/" \
     -H "Authorization: Bearer TOKEN"
```
📌 **Cas d’usage** : Automatiser **les déploiements**, **les mises à jour**, ou **la création d’infrastructure**.  

---

### **1.4 - Utilisation des collections Ansible**  
🎯 **Étendre les fonctionnalités avec des collections spécifiques**  

✅ **Installer une collection pour gérer AWS**  
```bash
ansible-galaxy collection install amazon.aws
```
✅ **Exemple : Créer une VM sur AWS avec Ansible**  
```yaml
- name: Créer une instance EC2
  hosts: localhost
  tasks:
    - amazon.aws.ec2_instance:
        name: "serveur-test"
        key_name: "ma_cle"
        region: "eu-west-1"
        instance_type: "t2.micro"
        image_id: "ami-12345678"
```
📌 **Cas d’usage** : Déploiement **Cloud** et **multi-cloud** avec Ansible.  

---

## **🔹 2. Sécurité et conformité**  
🎯 **Automatiser la sécurisation des serveurs**  

### **2.1 - Ansible Hardening**  
✅ **Appliquer les recommandations CIS (Center for Internet Security)**  
```yaml
- name: Sécurisation du système
  hosts: all
  become: true
  tasks:
    - name: Désactiver le root login SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^PermitRootLogin'
        line: 'PermitRootLogin no'
      notify: Restart SSH

    - name: Vérifier l’état du firewall
      service:
        name: firewalld
        state: started
        enabled: true
```
📌 **Cas d’usage** : Sécuriser les **serveurs Linux** automatiquement.  

---

### **2.2 - Automatisation des règles SELinux et Firewalld**  
✅ **Configurer SELinux en mode enforcing**  
```yaml
- name: Activer SELinux
  hosts: all
  become: true
  tasks:
    - name: Modifier la configuration SELinux
      selinux:
        policy: targeted
        state: enforcing
```
✅ **Gérer Firewalld avec Ansible**  
```yaml
- name: Ouvrir le port 443
  hosts: all
  become: true
  tasks:
    - firewalld:
        port: 443/tcp
        permanent: true
        state: enabled
```
📌 **Cas d’usage** : **Appliquer des règles de sécurité** à **grande échelle**.  

---

### **2.3 - Audit et conformité des systèmes**  
🎯 **Vérifier la conformité des serveurs avec un playbook Ansible**  

✅ **Exécuter un audit automatique**  
```yaml
- name: Vérification de la sécurité
  hosts: all
  become: true
  tasks:
    - name: Vérifier la présence de failles CVE
      shell: "grep -i cve /var/log/syslog"
      register: cve_log

    - name: Afficher les CVE détectées
      debug:
        msg: "{{ cve_log.stdout_lines }}"
```
🚀 **Résultat** : Un **rapport des failles** et **vulnérabilités** détectées.  

---

# **📌 Objectif Final**  
✅ Gérer **des centaines de serveurs** avec **Ansible Tower/AWX**  
✅ Automatiser avec **CI/CD et GitOps**  
✅ Assurer la **sécurité et la conformité**  
✅ Passer au **Niveau 5 : Architecte DevOps** 🚀  

💡 **Besoin d’exemples sur Kubernetes, Terraform, ou d’autres intégrations ?** 😃