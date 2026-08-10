Oui. Voici un tuto pratique centré sur les commandes que tu utiliseras le plus souvent.

## 1. Minikube : gérer ton cluster local

Démarrer un cluster :

```bash
minikube start
```

Vérifier son état :

```bash
minikube status
```

Arrêter le cluster sans le supprimer :

```bash
minikube stop
```

Redémarrer :

```bash
minikube start
```

Supprimer complètement le cluster :

```bash
minikube delete
```

Voir l'adresse IP du cluster :

```bash
minikube ip
```

Ouvrir le dashboard Kubernetes :

```bash
minikube dashboard
```

Lister les addons :

```bash
minikube addons list
```

Activer un addon, par exemple Ingress :

```bash
minikube addons enable ingress
```

Désactiver un addon :

```bash
minikube addons disable ingress
```

Se connecter directement à la VM/nœud Minikube :

```bash
minikube ssh
```

Afficher les logs Minikube :

```bash
minikube logs
```

### Commandes Minikube à retenir

```text
minikube start
minikube status
minikube stop
minikube delete
minikube dashboard
minikube ip
minikube addons list
minikube addons enable ingress
minikube ssh
minikube logs
```

---

# 2. kubectl : vérifier le cluster

`kubectl` est beaucoup plus important à maîtriser : c'est l'outil standard pour interagir avec Kubernetes.

Vérifier que `kubectl` communique avec ton cluster :

```bash
kubectl cluster-info
```

Voir les nœuds :

```bash
kubectl get nodes
```

Avec plus d'informations :

```bash
kubectl get nodes -o wide
```

Avec Minikube tu verras généralement quelque chose comme :

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   5m    v1.xx.x
```

---

# 3. Comprendre la commande kubectl

La structure générale est :

```text
kubectl <action> <ressource> <options>
```

Par exemple :

```bash
kubectl get pods
```

signifie :

```text
kubectl
  |
  +-- get      → récupérer/lister
       |
       +-- pods → ressource Kubernetes
```

Autres exemples :

```bash
kubectl get services
kubectl describe pod nginx
kubectl delete pod nginx
kubectl logs nginx
```

---

# 4. Créer des ressources avec YAML

Imaginons :

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: molocode

spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

Tu peux créer la ressource :

```bash
kubectl apply -f pod.yaml
```

Puis :

```bash
kubectl get pods
```

Résultat :

```text
NAME       READY   STATUS    RESTARTS   AGE
molocode   1/1     Running   0          10s
```

La commande fondamentale à retenir est :

```bash
kubectl apply -f fichier.yaml
```

Elle est préférable dans la plupart des workflows déclaratifs.

---

# 5. Lister les ressources

Pods :

```bash
kubectl get pods
```

Services :

```bash
kubectl get services
```

Deployments :

```bash
kubectl get deployments
```

Namespaces :

```bash
kubectl get namespaces
```

ConfigMaps :

```bash
kubectl get configmaps
```

Secrets :

```bash
kubectl get secrets
```

Ingress :

```bash
kubectl get ingress
```

Tout afficher :

```bash
kubectl get all
```

Très pratique :

```bash
kubectl get all -o wide
```

---

# 6. Obtenir plus d'informations avec describe

`get` donne une vue synthétique :

```bash
kubectl get pods
```

`describe` donne les détails :

```bash
kubectl describe pod molocode
```

C'est extrêmement important pour diagnostiquer un problème.

Tu trouveras notamment :

```text
Name
Namespace
Node
IP
Containers
Image
Ports
State
Events
```

La partie **Events** est souvent celle qu'il faut regarder en premier.

Même principe :

```bash
kubectl describe deployment mon-app

kubectl describe service mon-service

kubectl describe node minikube
```

---

# 7. Voir les logs

Commande essentielle :

```bash
kubectl logs molocode
```

Suivre les logs en temps réel :

```bash
kubectl logs -f molocode
```

C'est l'équivalent conceptuel de :

```bash
docker logs -f container
```

Si ton Pod possède plusieurs containers :

```bash
kubectl logs molocode -c nginx
```

---

# 8. Entrer dans un container

Très utile pour debugger :

```bash
kubectl exec -it molocode -- bash
```

Si l'image n'a pas Bash :

```bash
kubectl exec -it molocode -- sh
```

Tu te retrouves alors dans le container.

Par exemple :

```bash
ls

cd /usr/share/nginx/html

cat index.html
```

C'est très similaire à :

```bash
docker exec -it container bash
```

---

# 9. Supprimer une ressource

Supprimer un Pod :

```bash
kubectl delete pod molocode
```

Supprimer un Deployment :

```bash
kubectl delete deployment mon-app
```

Supprimer un Service :

```bash
kubectl delete service mon-service
```

Ou directement avec le YAML :

```bash
kubectl delete -f pod.yaml
```

---

# 10. Les Deployments

En pratique, tu créeras rarement des Pods directement.

On utilise plutôt :

```text
Deployment
    |
    +-- ReplicaSet
          |
          +-- Pod
          +-- Pod
          +-- Pod
```

Par exemple :

```bash
kubectl create deployment nginx --image=nginx
```

Puis :

```bash
kubectl get deployments
```

et :

```bash
kubectl get pods
```

---

# 11. Augmenter le nombre de Pods

Supposons :

```bash
kubectl get pods
```

avec :

```text
nginx-xxx   Running
```

Tu peux demander 3 instances :

```bash
kubectl scale deployment nginx --replicas=3
```

Puis :

```bash
kubectl get pods
```

Tu devrais obtenir :

```text
nginx-xxx   Running
nginx-yyy   Running
nginx-zzz   Running
```

Revenir à une instance :

```bash
kubectl scale deployment nginx --replicas=1
```

---

# 12. Exposer une application

Ton Pod n'est pas forcément directement accessible depuis l'extérieur.

Tu peux créer un Service :

```bash
kubectl expose deployment nginx \
  --type=NodePort \
  --port=80
```

Puis :

```bash
kubectl get services
```

Avec Minikube, une commande particulièrement pratique est :

```bash
minikube service nginx
```

Minikube peut alors t'aider à accéder au service depuis ta machine.

---

# 13. Port forwarding

C'est une commande que tu utiliseras énormément en développement.

Par exemple :

```bash
kubectl port-forward pod/molocode 8080:80
```

Cela signifie :

```text
PC                         Kubernetes

localhost:8080
      |
      v
+-------------+
| Port 8080   |
+------+------+
       |
       | port-forward
       v
+-------------+
| Pod         |
| molocode    |
|             |
| port 80     |
+-------------+
```

Tu peux ensuite ouvrir :

```text
http://localhost:8080
```

Tu peux également faire du port-forward sur un Service :

```bash
kubectl port-forward service/nginx 8080:80
```

---

# 14. Namespaces

Lister :

```bash
kubectl get namespaces
```

Version courte :

```bash
kubectl get ns
```

Créer :

```bash
kubectl create namespace dev
```

Lister les Pods du namespace :

```bash
kubectl get pods -n dev
```

Tout afficher :

```bash
kubectl get all -n dev
```

Supprimer :

```bash
kubectl delete namespace dev
```

---

# 15. Les raccourcis kubectl

Très utiles au quotidien :

| Ressource              | Version courte |
| ---------------------- | -------------- |
| pods                   | `po`           |
| services               | `svc`          |
| deployments            | `deploy`       |
| namespaces             | `ns`           |
| configmaps             | `cm`           |
| persistentvolumes      | `pv`           |
| persistentvolumeclaims | `pvc`          |

Donc :

```bash
kubectl get pods
```

peut devenir :

```bash
kubectl get po
```

Et :

```bash
kubectl get deployments
```

devient :

```bash
kubectl get deploy
```

Personnellement, pendant ton apprentissage, je te conseille d'utiliser les noms complets afin de bien mémoriser les concepts.

---

# 16. Observer les changements en temps réel

Très pratique :

```bash
kubectl get pods -w
```

`-w` signifie `watch`.

Par exemple, dans un terminal :

```bash
kubectl get pods -w
```

Dans un deuxième :

```bash
kubectl scale deployment nginx --replicas=5
```

Tu verras les Pods apparaître en direct.

---

# 17. Affichage détaillé

```bash
kubectl get pods -o wide
```

Cela ajoute notamment :

```text
IP
NODE
NOMINATED NODE
READINESS GATES
```

Tu peux également récupérer le YAML complet d'une ressource :

```bash
kubectl get pod molocode -o yaml
```

C'est très utile pour comprendre ce que Kubernetes stocke réellement.

---

# 18. Modifier une ressource directement

Tu peux faire :

```bash
kubectl edit deployment nginx
```

Cela ouvre le YAML dans ton éditeur.

Mais dans un vrai projet, évite autant que possible de modifier manuellement les ressources de cette manière.

Préfère :

```text
deployment.yaml
       ↓
modification Git
       ↓
kubectl apply -f deployment.yaml
```

Cela permet de conserver l'infrastructure versionnée.

---

# 19. Voir l'historique d'un Deployment

```bash
kubectl rollout history deployment nginx
```

Vérifier le déploiement :

```bash
kubectl rollout status deployment nginx
```

Redémarrer les Pods :

```bash
kubectl rollout restart deployment nginx
```

Très utile après un changement de configuration.

Et si un déploiement pose problème :

```bash
kubectl rollout undo deployment nginx
```

Kubernetes revient à la révision précédente.

---

# 20. Changer l'image d'un Deployment

Par exemple :

```bash
kubectl set image deployment/nginx nginx=nginx:1.28
```

Puis :

```bash
kubectl rollout status deployment nginx
```

et :

```bash
kubectl get pods
```

Kubernetes remplace progressivement les anciens Pods.

---

# 21. ConfigMaps et Secrets

Créer rapidement une ConfigMap :

```bash
kubectl create configmap app-config \
  --from-literal=ENV=dev
```

Voir :

```bash
kubectl get configmaps
```

Détails :

```bash
kubectl describe configmap app-config
```

Pour un Secret :

```bash
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=password
```

Puis :

```bash
kubectl get secrets
```

En environnement réel, évite bien sûr de mettre les mots de passe directement dans l'historique du terminal ou dans Git.

---

# 22. La commande `kubectl explain`

Une commande sous-estimée pour apprendre Kubernetes :

```bash
kubectl explain pod
```

Puis :

```bash
kubectl explain pod.spec
```

Encore plus précis :

```bash
kubectl explain pod.spec.containers
```

Tu peux donc explorer la structure d'un YAML Kubernetes directement depuis ton terminal.

---

# 23. Workflow de debugging à retenir

Quand ton application ne fonctionne pas, commence par :

```bash
kubectl get pods
```

Puis :

```bash
kubectl describe pod <pod>
```

Puis :

```bash
kubectl logs <pod>
```

Éventuellement :

```bash
kubectl logs -f <pod>
```

Et pour inspecter le container :

```bash
kubectl exec -it <pod> -- sh
```

Pour les événements du cluster :

```bash
kubectl get events
```

Un workflow très classique devient donc :

```text
Application KO
     |
     v
kubectl get pods
     |
     v
STATUS ?
     |
     +--> Pending
     |
     +--> CrashLoopBackOff
     |
     +--> ImagePullBackOff
     |
     v
kubectl describe pod
     |
     v
Events
     |
     v
kubectl logs
     |
     v
kubectl exec
```

---

# 24. Ton aide-mémoire

Pour commencer, mémorise surtout ces commandes :

```bash
# ==============================
# MINIKUBE
# ==============================

minikube start
minikube status
minikube stop
minikube delete

minikube dashboard

minikube addons list
minikube addons enable ingress

minikube service <service>


# ==============================
# CLUSTER
# ==============================

kubectl cluster-info

kubectl get nodes
kubectl get nodes -o wide


# ==============================
# RESSOURCES
# ==============================

kubectl get pods
kubectl get services
kubectl get deployments
kubectl get namespaces

kubectl get all
kubectl get all -o wide


# ==============================
# YAML
# ==============================

kubectl apply -f app.yaml

kubectl delete -f app.yaml


# ==============================
# DEBUG
# ==============================

kubectl describe pod <pod>

kubectl logs <pod>

kubectl logs -f <pod>

kubectl exec -it <pod> -- sh

kubectl get events


# ==============================
# DEPLOYMENT
# ==============================

kubectl scale deployment <name> --replicas=3

kubectl rollout status deployment <name>

kubectl rollout history deployment <name>

kubectl rollout restart deployment <name>

kubectl rollout undo deployment <name>


# ==============================
# ACCES APPLICATION
# ==============================

kubectl port-forward pod/<pod> 8080:80

kubectl port-forward service/<service> 8080:80


# ==============================
# APPRENTISSAGE
# ==============================

kubectl explain pod

kubectl explain pod.spec

kubectl explain pod.spec.containers
```

## Ordre d'apprentissage que je te conseille

Maintenant que tu as Minikube installé, le meilleur moyen de progresser est de construire **une petite application Kubernetes progressivement**, plutôt que d'apprendre toutes les commandes isolément :

```text
1. Minikube / kubectl
        ↓
2. Pod
        ↓
3. Deployment
        ↓
4. ReplicaSet / replicas
        ↓
5. Service
        ↓
6. ConfigMap
        ↓
7. Secret
        ↓
8. Volumes / PVC
        ↓
9. Namespace
        ↓
10. Ingress
        ↓
11. Probes
        ↓
12. Resources CPU/RAM
        ↓
13. Rolling Update / Rollback
        ↓
14. Debugging
        ↓
15. Helm
```

Un très bon exercice pour la suite serait de prendre **une petite application Spring Boot + Angular + PostgreSQL** et de la déployer progressivement sur Minikube : d'abord un Pod Nginx, puis Deployment + Service, ensuite Angular, Spring Boot, PostgreSQL, ConfigMaps/Secrets, volumes, Ingress et enfin Helm. C'est beaucoup plus proche de ce que tu rencontreras ensuite dans un environnement Kubernetes d'entreprise.
