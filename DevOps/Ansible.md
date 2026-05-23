# ANSIBLE

# 🧠 <ins>C’est quoi Ansible ?</ins>
Ansible est un outil d’automatisation très utilisé en DevOps.

Ansible sert à :
- automatiser des tâches (installation de logiciels, configuration, déploiement)
- gérer plusieurs serveurs en même temps
- garantir que tes systèmes sont dans l’état voulu

👉 Exemples concrets :
- installer nginx sur 50 serveurs
- créer des utilisateurs Linux
- déployer une application
- configurer Docker, Kubernetes, bases de données, etc.

# ⚙️ <ins>Principe fondamental</ins>
Ansible fonctionne selon 3 idées clés :

## 1.Agentless (sans agent)
- Aucun logiciel à installer sur les serveurs cibles
- Utilise SSH (Linux) ou WinRM (Windows)

👉 Tu as juste besoin de :
- Python sur la machine distante
- SSH actif

## 2.Déclaratif
Tu dis CE QUE tu veux, pas COMMENT le faire.

❌ Mauvais raisonnement :
```
“Installe nginx si pas installé, sinon ignore”
```

✅ Bon raisonnement Ansible :
```
“Je veux que nginx soit installé”
```
Ansible s’occupe du reste.

## 3.Idempotent
Tu peux exécuter un playbook 100 fois, le résultat sera le même.<br>
👉 Si tout est déjà correct → aucun changement


# 🧩 <ins>Les composants essentiels d’Ansible</ins>

## 1.Inventory (inventaire)
C’est la liste des machines à gérer.<br>
**Exemple simple** (inventory.ini)
```ini 
[web]
192.168.1.10
192.168.1.11

[db]
db1.example.com
```
Ou en YAML :
```yaml
all:
  children:
    web:
      hosts:
        web1:
        web2:
    db:
      hosts:
        db1:
```

## 2.Playbook
Un playbook est un fichier YAML qui décrit les actions à exécuter.
**Exemple ultra simple**
```yaml 
- name: Installer nginx
  hosts: web
  become: true
  tasks:
    - name: Installer le paquet nginx
      apt:
        name: nginx
        state: present

```
📌 Traduction :
```Sur les serveurs du groupe web, installe nginx en root.```

## 3.Tasks
Une task = une action.
```yaml
- name: Créer un utilisateur
  user:
    name: alice
    state: present
```

## 4.Modules
Ansible fonctionne grâce à des modules (déjà fournis).<br>
**Modules très courants**<br>
| Module             | Sert à                |
| ------------------ | --------------------- |
| `apt`              | gérer paquets Debian  |
| `yum`              | paquets RedHat        |
| `service`          | gérer services        |
| `copy`             | copier fichiers       |
| `template`         | fichiers dynamiques   |
| `user`             | utilisateurs          |
| `file`             | permissions, dossiers |
| `docker_container` | Docker                |
| `k8s`              | Kubernetes            |

👉 Tu n’écris presque jamais de shell.

## 🔁 Exemple complet réaliste
Objectif :
- Installer nginx
- Démarrer le service
- Déployer une page HTML

```yaml
- name: Setup serveur web
  hosts: web
  become: true

  tasks:
    - name: Installer nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Démarrer nginx
      service:
        name: nginx
        state: started
        enabled: true

    - name: Copier la page web
      copy:
        content: "<h1>Hello Ansible 🚀</h1>"
        dest: /var/www/html/index.html

```

## 📦 Variables
Les variables rendent tes playbooks flexibles.
```yaml
vars:
  app_port: 8080
```

Utilisation :
```yaml
- name: Afficher le port
  debug:
    msg: "Le port est {{ app_port }}"
```


## 📁 Templates (Jinja2)
Tu peux générer des fichiers dynamiques.<br>
**nginx.conf.j2**
```nginx
server {
  listen {{ port }};
  server_name {{ domain }};
}
```

**Playbook**
```yaml
- name: Config nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/default
```


## 🧠 Handlers (actions déclenchées)
Très utile pour redémarrer un service seulement si nécessaire.
```yaml
tasks:
  - name: Modifier config nginx
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Restart nginx

handlers:
  - name: Restart nginx
    service:
      name: nginx
      state: restarted
	  
```


## 📦 Roles (organisation propre)
Quand ça devient gros, on utilise des **roles**.

Structure :
```text
roles/
 └── nginx/
     ├── tasks/main.yml
     ├── handlers/main.yml
     ├── templates/
     ├── vars/
     └── defaults/
```

Playbook :
```yaml 
- hosts: web
  roles:
    - nginx
```
👉 C’est LE standard en entreprise


## 🧪 Commandes utiles
**Tester la connexion**
```bash
ansible all -m ping
```

**Lancer un playbook**
```bash
ansible-playbook site.yml
```

**Mode simulation**
```bash
ansible-playbook site.yml --check
```


## ⚠️ Bonnes pratiques
✔ Pas de shell si un module existe<br>
✔ Toujours utiliser become: true proprement<br>
✔ Utiliser des roles<br>
✔ Séparer dev / prod<br>
✔ Versionner avec Git<br>


## 🆚 Ansible vs autres outils
| Outil     | Différence           |
| --------- | -------------------- |
| Ansible   | simple, agentless    |
| Puppet    | plus complexe, agent |
| Chef      | très puissant, lourd |
| Terraform | infra, pas config    |
| Bash      | pas idempotent       |


## 🎯 Quand utiliser Ansible ?
✅ Configuration système<br>
✅ Déploiement d’applications<br>
✅ Automatisation DevOps<br>
❌ Orchestration complexe temps réel<br>
❌ Très gros clusters ultra dynamiques<br>