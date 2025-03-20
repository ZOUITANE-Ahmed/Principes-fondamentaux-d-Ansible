## 🔹 **Le module `file` dans Ansible**  
Le module `file` permet de **gérer les fichiers et répertoires** sur des machines distantes. Il permet de :  
✅ Créer ou supprimer des fichiers et des répertoires.  
✅ Modifier les permissions, le propriétaire et le groupe.  
✅ Créer des liens symboliques.  

---

### 📌 **Exemples d'utilisation du module `file`**  

#### **1️⃣ Créer un répertoire**
```yaml
- name: Créer un répertoire
  hosts: centos1
  tasks:
    - name: Assurer l'existence de /opt/mon_dossier
      ansible.builtin.file:
        path: /opt/mon_dossier
        state: directory
        mode: '0755'
```
🔹 Crée le répertoire `/opt/mon_dossier` avec des permissions `0755`.

---

#### **2️⃣ Créer un fichier vide**
```yaml
- name: Créer un fichier vide
  hosts: centos1
  tasks:
    - name: Assurer l'existence de /tmp/exemple.txt
      ansible.builtin.file:
        path: /tmp/exemple.txt
        state: touch
```
🔹 Si le fichier `/tmp/exemple.txt` n'existe pas, il sera créé.

---

#### **3️⃣ Modifier les permissions d'un fichier**
```yaml
- name: Modifier les permissions d'un fichier
  hosts: centos1
  tasks:
    - name: Définir les permissions de /etc/config.conf
      ansible.builtin.file:
        path: /etc/config.conf
        mode: '0644'
        owner: root
        group: root
```
🔹 Définit le fichier `/etc/config.conf` avec les permissions `0644`, propriétaire `root`, et groupe `root`.

---

#### **4️⃣ Créer un lien symbolique**
```yaml
- name: Créer un lien symbolique
  hosts: centos1
  tasks:
    - name: Lier /usr/local/bin/monapp à /opt/monapp/app.sh
      ansible.builtin.file:
        src: /opt/monapp/app.sh
        dest: /usr/local/bin/monapp
        state: link
```
🔹 Crée un lien symbolique `/usr/local/bin/monapp` pointant vers `/opt/monapp/app.sh`.

---

#### **5️⃣ Supprimer un fichier ou un répertoire**
```yaml
- name: Supprimer un fichier
  hosts: centos1
  tasks:
    - name: Supprimer /tmp/exemple.txt
      ansible.builtin.file:
        path: /tmp/exemple.txt
        state: absent
```
🔹 Supprime le fichier `/tmp/exemple.txt` s'il existe.

```yaml
- name: Supprimer un répertoire
  hosts: centos1
  tasks:
    - name: Supprimer /opt/mon_dossier
      ansible.builtin.file:
        path: /opt/mon_dossier
        state: absent
```
🔹 Supprime le répertoire `/opt/mon_dossier` et son contenu.

---

### ✅ **Points Clés**
- `state: directory` → Crée un répertoire.  
- `state: touch` → Crée un fichier vide.  
- `state: link` → Crée un lien symbolique.  
- `state: absent` → Supprime un fichier ou un répertoire.  
- Vous pouvez définir les **permissions (`mode`), propriétaire (`owner`), et groupe (`group`)**.  

---

### 🚀 **Prochaine Étape**
Voulez-vous combiner `file` avec d'autres modules comme `copy` ou `template` pour une automatisation plus avancée ? 😊