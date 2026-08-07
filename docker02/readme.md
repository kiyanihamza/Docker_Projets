# Déploiement Nginx sur Application Server 2

Ce README décrit la procédure de déploiement d'un conteneur Nginx nommé `nginx_2` sur Application Server 2 en utilisant l'image `nginx:alpine`.

## Prérequis
- Docker doit être installé et en cours d'exécution sur Application Server 2.
- Un accès au shell du serveur doit être disponible.

## Étapes de déploiement
Exécuter les commandes suivantes sur Application Server 2 :

```bash
docker pull nginx:alpine
docker run -d --name nginx_2 -p 80:80 nginx:alpine
```

## Vérification
Pour vérifier que le conteneur est bien en cours d'exécution :

```bash
docker ps --filter "name=nginx_2"
docker inspect -f '{{.State.Status}}' nginx_2
```

Résultat attendu :
- Le conteneur apparaît dans `docker ps`.
- Le statut retourné par `docker inspect` est `running`.

## Nettoyage si nécessaire
Si un conteneur portant le même nom existe déjà, le supprimer au préalable :

```bash
docker rm -f nginx_2
```
