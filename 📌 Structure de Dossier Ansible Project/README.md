Si tu souhaites créer un modèle de dossier pour organiser un projet Ansible, voici une structure recommandée pour t'aider à bien organiser tes fichiers et répertoires.

### **Structure de Dossier Ansible**

```bash
ansible_project/
│
├── ansible.cfg                  # Fichier de configuration Ansible
├── inventory/                   # Fichiers d'inventaire
│   └── hosts                    # Fichier d'inventaire avec les informations des hôtes
│
├── playbooks/                   # Répertoire des playbooks
│   ├── main.yml                 # Playbook principal
│   ├── deploy_nginx.yml         # Exemple de playbook pour déployer Nginx
│   └── configure_nginx.yml      # Exemple de playbook pour configurer Nginx
│
├── roles/                       # Répertoire des rôles
│   ├── common/                  # Rôle pour la configuration générale
│   │   ├── tasks/               # Tâches pour le rôle common
│   │   │   └── main.yml
│   │   ├── templates/           # Modèles Jinja2 pour ce rôle
│   │   │   └── nginx.conf.j2
│   │   └── files/               # Fichiers statiques pour ce rôle
│   │       └── index.html
│   └── nginx/                   # Rôle spécifique pour Nginx
│       ├── tasks/
│       ├── templates/
│       └── files/
│
├── templates/                   # Modèles Jinja2 généraux
│   └── nginx.conf.j2
│
└── README.md                    # Documentation du projet
```

---

### **Explication des répertoires :**

1. **`ansible.cfg`** : Le fichier de configuration d'Ansible pour définir les comportements par défaut comme l'inventaire, le chemin des rôles, etc.

2. **`inventory/`** : Contient les fichiers d'inventaire des hôtes. Dans ce dossier, tu peux définir les groupes de machines sur lesquelles exécuter les playbooks.

3. **`playbooks/`** : Contient les playbooks Ansible. Chaque playbook est un fichier YAML qui décrit les tâches à exécuter sur les hôtes distants.  
   - `main.yml` : Peut être le playbook principal.
   - `deploy_nginx.yml` : Playbook spécifique pour déployer Nginx.
   - `configure_nginx.yml` : Playbook pour configurer Nginx après son installation.

4. **`roles/`** : Contient des rôles pour séparer les tâches en unités réutilisables et modulaires. Par exemple :
   - **`common/`** : Configuration générale commune à tous les hôtes.
   - **`nginx/`** : Installation et configuration de Nginx.

5. **`templates/`** : Contient les fichiers modèles Jinja2 que tu peux utiliser dans tes playbooks avec le module `template`.

6. **`files/`** : Contient les fichiers statiques qui seront copiés sur les machines distantes via le module `copy`.

7. **`README.md`** : Documentation du projet, où tu peux décrire la structure, les rôles, et comment utiliser les playbooks.

---

### **Exemple de contenu de `main.yml` :**

```yaml
- name: Déployer Nginx sur les serveurs web
  hosts: web
  become: yes  # Utilisation de sudo pour exécuter les commandes avec les droits d'administrateur
  roles:
    - nginx
```

### **Exemple de rôle `nginx` (role/nginx/tasks/main.yml) :**

```yaml
- name: Installer Nginx
  ansible.builtin.yum:
    name: nginx
    state: present

- name: Démarrer Nginx
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: yes
```

---

### **Prochaine Étape**
Si tu as une idée spécifique sur un projet que tu veux organiser, je peux t'aider à définir des rôles et playbooks en fonction de tes besoins ! 😊