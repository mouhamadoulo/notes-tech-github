# Commandes utiles Docker 

## Lister les conteneurs 

```
docker ps
```

```
docker ps -a
```

## Supprimer tous les volumes 

```
docker volume rm $(docker volume ls -q)
```

## Supprimer toutes les images 

```
docker rmi $(docker images -q)
```
