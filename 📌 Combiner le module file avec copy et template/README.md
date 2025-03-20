### 🔹 **Combiner le module `file` avec `copy` et `template`**  

L'association de ces modules permet une **gestion avancée des fichiers et configurations** sur des machines distantes.  

---

## 1️⃣ **Module `copy`** – Copier un fichier sur une machine distante  

Le module `copy` est utilisé pour **transférer un fichier local vers une machine distante** tout en définissant ses permissions et son propriétaire.  

#### ✅ **Exemple : Copier un fichier de configuration**  
```yaml
- name: Copier un fichier vers la machine distante
  hosts: centos1
  tasks:
    - name: Copier /local/config.conf vers /etc/config.conf
      ansible.builtin.copy:
        src: ./config.conf
        dest: /etc/config.conf
        owner: root
        group: root
        mode: '0644'
```
🔹 **Explication** :  
✔ `src: ./config.conf` → Fichier local à copier.  
✔ `dest: /etc/config.conf` → Destination sur la machine distante.  
✔ `owner: root`, `group: root` → Définit le propriétaire et le groupe.  
✔ `mode: '0644'` → Permissions du fichier.  

---

## 2️⃣ **Module `template`** – Générer un fichier dynamique  

Le module `template` permet **d'utiliser des variables dans un fichier de configuration** grâce aux fichiers **Jinja2 (`.j2`)**.  

#### ✅ **Exemple : Générer un fichier de configuration Nginx**  

### 🔹 **1. Créer un modèle Jinja2 (`nginx.conf.j2`)**
```jinja2
server {
    listen 80;
    server_name {{ domaine }};
    root {{ dossier_racine }};
    index index.html;
}
```

### 🔹 **2. Utiliser `template` dans un playbook**  
```yaml
- name: Générer un fichier de configuration Nginx
  hosts: centos1
  vars:
    domaine: monsite.com
    dossier_racine: /var/www/html
  tasks:
    - name: Créer /etc/nginx/sites-available/monsite.conf
      ansible.builtin.template:
        src: ./nginx.conf.j2
        dest: /etc/nginx/sites-available/monsite.conf
        owner: root
        group: root
        mode: '0644'
```
🔹 **Explication** :  
✔ Le fichier `nginx.conf.j2` est utilisé comme modèle.  
✔ Les variables `domaine` et `dossier_racine` sont remplacées par leurs valeurs (`monsite.com`, `/var/www/html`).  
✔ Le fichier est placé dans `/etc/nginx/sites-available/`.  

---

## 3️⃣ **Combinaison avec `file` pour créer un lien symbolique**  

Après avoir copié ou généré un fichier, on peut utiliser `file` pour **créer un lien symbolique**.  

```yaml
- name: Activer le site Nginx avec un lien symbolique
  hosts: centos1
  tasks:
    - name: Lier /etc/nginx/sites-enabled/monsite.conf à /etc/nginx/sites-available/monsite.conf
      ansible.builtin.file:
        src: /etc/nginx/sites-available/monsite.conf
        dest: /etc/nginx/sites-enabled/monsite.conf
        state: link
```
🔹 Ce lien permet d’activer la configuration Nginx.  

---

## ✅ **Résumé**
| Module | Fonction principale |
|--------|--------------------|
| **file** | Créer/modifier des fichiers, répertoires et liens symboliques. |
| **copy** | Copier un fichier statique depuis la machine locale vers la distante. |
| **template** | Générer un fichier basé sur un modèle dynamique avec Jinja2. |
