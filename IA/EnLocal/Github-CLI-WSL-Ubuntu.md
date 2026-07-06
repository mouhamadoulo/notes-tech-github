Pour installer **GitHub CLI (`gh`)** sur **WSL Ubuntu**, suis ces étapes dans ton terminal :

### 1. Mettre à jour les paquets

```bash
sudo apt update
sudo apt install -y curl
```

### 2. Ajouter le dépôt officiel GitHub CLI

```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg

sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
  | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
```

### 3. Installer `gh`

```bash
sudo apt update
sudo apt install gh
```

### 4. Vérifier l'installation

```bash
gh --version
```

Tu devrais voir quelque chose comme :

```text
gh version 2.xx.x
```

### 5. Se connecter à GitHub

Lance ensuite :

```bash
gh auth login
```

Choisis les options suivantes :

* **GitHub.com**
* **HTTPS**
* **Login with a web browser** (recommandé)

`gh` affichera un code et ouvrira (ou te demandera d'ouvrir) une page GitHub pour autoriser la connexion.

---

### Alternative (Ubuntu 24.04+)

Si la version du dépôt Ubuntu est suffisamment récente, tu peux simplement faire :

```bash
sudo apt update
sudo apt install gh
```

Puis vérifier :

```bash
gh --version
```

Si la version est ancienne ou si le paquet n'est pas disponible, utilise la méthode du dépôt officiel décrite plus haut.
