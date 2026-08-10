Oui. Sur Windows + WSL2 Ubuntu, je te conseille une architecture simple : **Docker Desktop comme moteur de conteneurs**, avec intégration WSL2 activée, puis Minikube installé soit côté Windows, soit côté Ubuntu/WSL. Le driver Docker est officiellement supporté par Minikube sur `amd64` et `arm64`; sous WSL2, le support est indiqué comme expérimental. ([minikube][1])

## 1. Architecture recommandée

```text
Windows 11
│
├── Docker Desktop
│      └── WSL2 backend
│
├── PowerShell
│      ├── docker
│      ├── kubectl
│      └── minikube
│
└── WSL2 Ubuntu
       ├── docker CLI
       ├── kubectl
       └── minikube
```

Évite de lancer **deux clusters Minikube en même temps** au début. Si ton objectif est surtout DevOps/Linux/Kubernetes, je privilégierais **Minikube depuis WSL Ubuntu**.

Minikube demande au minimum environ **2 CPU, 2 Go de RAM et 20 Go de disque disponible**. ([minikube][2])

## 2. Vérifier Docker Desktop

Dans PowerShell :

```powershell
docker --version
docker info
```

Puis :

```powershell
docker run hello-world
```

Docker doit fonctionner avec des **Linux containers** :

```powershell
docker info --format '{{.OSType}}'
```

Résultat attendu :

```text
linux
```

C'est particulièrement important avec le driver Docker de Minikube sous Windows. ([minikube][1])

Dans Docker Desktop, vérifie aussi :

```text
Settings
  → Resources
    → WSL Integration
      → Enable integration with my default WSL distro
      → Ubuntu : ON
```

## 3. Installer Minikube sur Windows

Le plus simple est `winget`.

Ouvre **PowerShell** :

```powershell
winget install Kubernetes.minikube
```

Ferme/réouvre le terminal puis :

```powershell
minikube version
```

Tu devrais obtenir quelque chose du genre :

```text
minikube version: v1.38.x
```

La documentation officielle indique actuellement Minikube `v1.38.1` comme release stable publiée en février 2026. ([minikube][3])

[Documentation officielle d'installation Minikube](https://minikube.sigs.k8s.io/docs/start/?utm_source=chatgpt.com)

## 4. Installer kubectl sur Windows

Avec winget :

```powershell
winget install Kubernetes.kubectl
```

Vérifie :

```powershell
kubectl version --client
```

## 5. Créer ton premier cluster depuis Windows

Avec Docker Desktop démarré :

```powershell
minikube start --driver=docker
```

Tu peux aussi définir Docker comme driver par défaut :

```powershell
minikube config set driver docker
```

Puis les fois suivantes :

```powershell
minikube start
```

C'est précisément la commande recommandée par Minikube pour utiliser le driver Docker. ([minikube][1])

Vérifie :

```powershell
minikube status
```

Puis :

```powershell
kubectl get nodes
```

Tu devrais voir quelque chose comme :

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   1m    v1.xx.x
```

Et :

```powershell
kubectl get pods -A
```

## 6. Installer Minikube dans WSL Ubuntu

Passe maintenant dans Ubuntu :

```powershell
wsl
```

Vérifie d'abord l'architecture :

```bash
uname -m
```

Si tu obtiens :

```text
x86_64
```

installe la version AMD64 :

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

rm minikube-linux-amd64
```

Si tu obtiens :

```text
aarch64
```

utilise plutôt :

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-arm64

sudo install minikube-linux-arm64 /usr/local/bin/minikube

rm minikube-linux-arm64
```

Puis :

```bash
minikube version
```

La commande officielle Linux AMD64 est également documentée par Minikube. ([minikube][2])

## 7. Vérifier Docker depuis WSL

C'est une étape importante :

```bash
docker version
```

Puis :

```bash
docker ps
```

Si ça fonctionne, ton Ubuntu WSL peut utiliser Docker Desktop.

Tu peux tester :

```bash
docker run hello-world
```

Si `docker` n'est pas reconnu, retourne dans Docker Desktop et vérifie :

```text
Settings
→ Resources
→ WSL Integration
→ Ubuntu
→ ON
```

puis :

```powershell
wsl --shutdown
```

Relance Docker Desktop puis Ubuntu.

## 8. Installer kubectl dans WSL

Tu peux installer directement le binaire officiel :

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/$(dpkg --print-architecture)/kubectl"
```

Puis :

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
```

Vérifie :

```bash
kubectl version --client
```

## 9. Démarrer Minikube depuis WSL

Dans Ubuntu :

```bash
minikube start --driver=docker
```

Ou :

```bash
minikube config set driver docker
minikube start
```

Puis :

```bash
minikube status
```

et :

```bash
kubectl get nodes
```

Le driver Docker évite d'avoir à installer un hyperviseur Linux supplémentaire et est le choix le plus simple dans ce type de configuration. ([minikube][1])

## 10. Tester Kubernetes

Une fois ton cluster lancé, crée une petite application :

```bash
kubectl create deployment nginx --image=nginx
```

Vérifie :

```bash
kubectl get deployments
```

Puis :

```bash
kubectl get pods
```

Tu devrais avoir :

```text
NAME                     READY   STATUS    RESTARTS
nginx-xxxxxxxxxx-xxxxx    1/1     Running   0
```

Expose nginx :

```bash
kubectl expose deployment nginx \
  --type=NodePort \
  --port=80
```

Puis :

```bash
kubectl get services
```

Et :

```bash
minikube service nginx --url
```

Avec Docker sur Windows/WSL, `minikube service ... --url` est justement utile car l'IP du nœud n'est pas nécessairement directement accessible depuis l'hôte. ([minikube][4])

## 11. Commandes Minikube à connaître

Voici celles que tu utiliseras énormément :

```bash
# Démarrer
minikube start

# État
minikube status

# IP du cluster
minikube ip

# Dashboard Kubernetes
minikube dashboard

# Arrêter
minikube stop

# Supprimer complètement le cluster
minikube delete

# Voir les addons
minikube addons list

# Activer ingress
minikube addons enable ingress

# Informations cluster
kubectl cluster-info

# Nodes
kubectl get nodes

# Tous les pods
kubectl get pods -A
```

Minikube fournit effectivement de nombreux addons activables via `minikube addons enable`, notamment pour expérimenter avec des fonctionnalités Kubernetes supplémentaires. ([minikube][5])

## Windows ou WSL : lequel utiliser ?

Pour ton apprentissage DevOps, je partirais sur :

```text
Windows 11
        │
        ▼
Docker Desktop
        │
        │ WSL Integration
        ▼
┌─────────────────────────────┐
│ Ubuntu WSL2                 │
│                             │
│ docker CLI                  │
│ kubectl                     │
│ minikube                    │
│ helm                        │
│                             │
│ minikube start              │
│       --driver=docker       │
└──────────────┬──────────────┘
               │
               ▼
        Kubernetes local
               │
     ┌─────────┼─────────┐
     ▼         ▼         ▼
   Pods     Services   Ingress
```

C'est plus proche de ce que tu retrouveras ensuite sur des serveurs Linux et dans les environnements Kubernetes réels.

Je te déconseille donc d'apprendre en parallèle avec **un Minikube Windows + un autre Minikube WSL**. Installe éventuellement les deux clients pour tester, mais utilise principalement **WSL2 + Ubuntu + Docker driver + Minikube + kubectl + Helm** comme environnement DevOps local. ([minikube][1])

Une fois installé, une très bonne suite serait de faire un mini-lab progressif : **Deployment → Pod → Service → ConfigMap → Secret → Volume → Ingress → Helm**, puis déployer une application **Spring Boot + Angular + PostgreSQL** dessus.

[1]: https://minikube.sigs.k8s.io/docs/drivers/docker/?utm_source=chatgpt.com "docker | minikube"
[2]: https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fhomebrew&utm_source=chatgpt.com "minikube start | minikube"
[3]: https://minikube.sigs.k8s.io/docs/?utm_source=chatgpt.com "Welcome! | minikube"
[4]: https://minikube.sigs.k8s.io/docs/handbook/accessing/?utm_source=chatgpt.com "Accessing apps | minikube"
[5]: https://minikube.sigs.k8s.io/docs/handbook/deploying/?utm_source=chatgpt.com "Deploying apps | minikube"
