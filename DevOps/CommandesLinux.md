# TOP COMMANDES LINUX
Pour quelqu’un qui fait du DevOps, Linux est l’environnement principal pour gérer des serveurs, <br>
des conteneurs et des pipelines CI/CD. <br>
Il faut donc maîtriser certaines commandes essentielles pour naviguer, gérer les processus, <br>
manipuler les fichiers et diagnostiquer les systèmes.

Voici quelques commandes Linux les plus importantes pour le DevOps, classées par catégories.

# 1. <ins>Navigation dans le système</ins>

## pwd
Affiche le dossier courant.
```Bash
pwd
```
Exemple :
```
/home/devops/projects
```

## ls
Liste les fichiers.
```Bash
ls
```
Options importantes :
```Bash
ls -l     # mode détaillé
ls -a     # fichiers cachés
ls -lh    # taille lisible
```
Exemple :
```
-rw-r--r-- 1 user user 2.3K config.yml
```

## cd
Changer de dossier.
```Bash
cd /var/log
```
Retour au dossier précédent :
```Bash
cd ..
```
Retour au home :
```Bash
cd ~
```

# 2. <ins>Manipulation des fichiers</ins>

## cp
Copier un fichier.
```Bash
cp file.txt backup.txt
```

Copier un dossier :
```Bash
cp -r folder1 folder2
```

## mv
Déplacer ou renommer.
```Bash
mv file.txt archive/
```

Renommer :
```Bash
mv old.txt new.txt
```

## rm
Supprimer.
```Bash
rm file.txt
```

Supprimer un dossier :
```Bash
rm -r folder
```

⚠️ Très dangereux :
```Bash
rm -rf folder
```

## touch
Créer un fichier vide.
```Bash
touch app.log
```

# 3. <ins>Lecture de fichiers (très utile pour les logs)</ins>

## cat
Afficher un fichier.
```Bash
cat config.yml
```

## less
Lire un fichier long.
```Bash
less app.log
```
Navigation :
```
q  -> quitter
/  -> rechercher
```

## tail
Lire la fin d’un fichier.
```Bash
tail app.log
```
Super utile pour les logs.<br>
Mode temps réel :
```Bash
tail -f app.log
```
Très utilisé en DevOps.

# 4. <ins>Recherche</ins>

## grep
Chercher dans un fichier.
```Bash
grep "error" app.log
```
Recherche récursive :
```Bash
grep -r "password" .
```
Très utilisé pour analyser les logs.

## find
Chercher un fichier.
```Bash
find /var -name "*.log"
```

# 5. <ins>Gestion des processus</ins>

## ps
Afficher les processus.
```Bash
ps aux
```

## top
Monitorer le système en temps réel.
```Bash
top
```
Alternative moderne :
```Bash
htop
```

## kill
Arrêter un processus.
```Bash
kill 1234
```
Forcer :
```Bash
kill -9 1234
```

# 6. <ins>Permissions (très important)</ins>
Linux fonctionne avec des permissions :
```
r = read
w = write
x = execute
```

## chmod
Modifier les permissions.
```Bash
chmod +x script.sh
```
Exemple DevOps : rendre un script exécutable.

## chown
Changer le propriétaire.
```Bash
chown user:group file.txt
```

# 7. <ins>Réseau</ins>
Très important pour DevOps.

## ping
Tester la connexion.
```Bash 
ping google.com
```

## curl
Faire des requêtes HTTP.
```Bash
curl https://api.github.com
```
Tester une API :
```Bash
curl -X POST http://localhost:8080/api
```

## netstat
Voir les ports ouverts.
```Bash 
netstat -tuln
```
Alternative moderne :
```Bash 
ss -tuln
```

# 8. <ins>Gestion disque</ins>

## df
Espace disque.
```Bash
df -h
```

## du
Taille d’un dossier.
```Bash 
du -sh logs/
```

# 9. <ins>Gestion des services (serveurs)</ins>
Sur les distributions modernes (Ubuntu, Debian, etc.) :

## systemctl
Démarrer un service :
```Bash 
systemctl start nginx
```
Arrêter :
```Bash
systemctl stop nginx
```
Statut :
```Bash 
systemctl status nginx
```

# 10. <ins>Commandes indispensables DevOps</ins>

## ssh
Connexion à un serveur.
```Bash 
ssh user@192.168.1.10
```

## scp
Copier un fichier vers un serveur.
```Bash 
scp file.txt user@server:/home/user
```

## wget
Télécharger un fichier.
```Bash 
wget https://example.com/file.zip
```

# 💡 <ins>Conseil DevOps</ins>
Apprends aussi les pipes (|), car elles rendent Linux extrêmement puissant.

Exemple :
```Bash 
cat app.log | grep ERROR
```
Ou :
```Bash 
ps aux | grep nginx
```