# KUBERNETES
<div align="center">
	<img src="images/kubernetes.png" height="400" width="400" />
</div>

## 🌍 <ins>Kubernetes (K8s) — c’est quoi exactement ?</ins>
Kubernetes est une plateforme d’orchestration de conteneurs.

👉 En clair :
```
Kubernetes déploie, gère, met à l’échelle et répare automatiquement des applications conteneurisées (souvent Docker).
```
Si Docker est une boîte, Kubernetes est le chef d’orchestre qui gère des milliers de boîtes sur plein de machines.

## 🤔 <ins>Pourquoi Kubernetes existe ?</ins>
Sans Kubernetes :
- tu lances tes conteneurs à la main
- tu gères les pannes toi-même
- tu fais les mises à jour en stress
- tu pries pour que ça scale 😅

Avec Kubernetes :
- 🔄 redémarrage automatique
- 📈 scaling automatique
- 🚀 déploiements progressifs
- 💥 tolérance aux pannes
- 🌐 load balancing intégré

## 🧱 <ins>Les concepts fondamentaux (ultra important)</ins>
<div align="center">
	<img src="images/kubernetes2.png" height="600" width="600" />
</div>

**Cluster**<br>
Un cluster Kubernetes = un ensemble de machines (nodes)
- Control Plane (cerveau)
- Worker Nodes (muscles)
```java 
Cluster
 ├── Control Plane
 └── Worker Nodes (xN)
```

**Node**<br>
Une node = une machine (VM ou physique)
- exécute des Pods
- a un agent appelé kubelet
```bash 
kubectl get nodes
```

**Pod (la brique de base)**<br>
Un Pod = 👉 1 ou plusieurs conteneurs qui partagent le même réseau et stockage<br>
⚠️ Tu ne crées presque jamais des Pods directement en prod.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mon-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest

```

**Deployment (Le plus utilisé)**<br>
Un Deployment :
- gère les Pods
- assure la haute dispo
- permet les mises à jour sans downtime
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25

```

👉 Appliquer :
```bash
kubectl apply -f deployment.yaml
```

👉 Voir :
```bash
kubectl get deployments
kubectl get pods
```

**ReplicaSet**<br>
Derrière un Deployment, Kubernetes crée un ReplicaSet.<br>
Son rôle :
- garantir X copies identiques d’un Pod
Tu ne le touches presque jamais directement.

**Service (réseau interne & externe)**<br>
Les Pods ont des IP éphémères → pas fiable<br>
👉 Service = IP stable + load balancing

*Service interne (ClusterIP) :*
```yaml 
kind: Service
apiVersion: v1
metadata:
  name: nginx-svc
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80

```

*Service exposé vers l’extérieur :*
```yaml
type: NodePort
```
ou
```yaml
type: LoadBalancer
```

**Ingress (HTTP propre)**<br>
Un Ingress gère :
- domaines
- HTTPS
- routing
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: monapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-svc
            port:
              number: 80

```

## 📦 <ins>Configuration & secrets</ins>
**ConfigMap (config non sensible)**
```bash 
kubectl create configmap app-config --from-literal=ENV=prod
```

Utilisation dans un Pod :
```yaml
env:
- name: ENV
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: ENV

```

**Secret (mots de passe, tokens)**
```bash 
kubectl create secret generic db-secret --from-literal=password=supersecret
```

## 💾 <ins>Stockage</ins>
**Volume**<br>
Stockage attaché au Pod (éphémère ou persistant)

**PersistentVolume (PV) + PVC**
```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: data-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## ⚙️ <ins>Autoscaling</ins>
Horizontal Pod Autoscaler (HPA)
```bash
kubectl autoscale deployment nginx-deploy --cpu-percent=50 --min=2 --max=10
```
👉 Kubernetes scale automatiquement selon la charge.

## 🔁 <ins>Mises à jour sans coupure</ins>
```bash
kubectl set image deployment/nginx-deploy nginx=nginx:1.26
```

Rollback :
```bash
kubectl rollout undo deployment/nginx-deploy
```

## 🔍 <ins>Debug & monitoring</ins>
```bash
kubectl logs mon-pod
kubectl describe pod mon-pod
kubectl exec -it mon-pod -- bash
```

Voir tout :
```bash
kubectl get all
```

## 🧠 Architecture interne (vue rapide)
- API Server → point central
- Scheduler → décide où placer les Pods
- Controller Manager → assure l’état désiré
- etcd → base clé/valeur (état du cluster)

## 🧪 Kubernetes en local
Pour pratiquer :
- minikube (simple)
- kind (Docker-based)
- k3s (léger, prod-friendly)
```bash 
minikube start
kubectl get nodes
```

## 🚀 Kubernetes en production
Clouds populaires :
- GKE (Google)
- EKS (AWS)
- AKS (Azure)

Outils autour :
- Helm (package manager)
- ArgoCD / Flux (GitOps)
- Prometheus / Grafana (monitoring)

## 🧩 Résumé ultra rapide
| Concept    | Rôle              |
| ---------- | ----------------- |
| Pod        | unité de base     |
| Deployment | gestion des Pods  |
| Service    | réseau stable     |
| Ingress    | HTTP / HTTPS      |
| ConfigMap  | config            |
| Secret     | données sensibles |
| HPA        | autoscaling       |
