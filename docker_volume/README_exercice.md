# Exercice : Conteneurisation Docker (apprentissage)

But :
- Sur le serveur "App Server 1" (Stratos DC), créer et lancer un conteneur Docker nommé `media` basé sur l'image `nginx`.
- Monter le volume hôte `/opt/itadmin` dans le conteneur sur `/home`.
- Copier le fichier `/tmp/sample.txt` vers `/opt/itadmin` sur le serveur hôte avant de lancer le conteneur.
- Laisser le conteneur en état `running` pour tests.

Prérequis :
- Accès SSH à `App Server 1` avec droits `sudo` (ou root).
- Docker installé et opérationnel sur ce serveur.

Étapes détaillées (commande par commande)

1) Préparer le volume hôte et copier le fichier

```bash
# Créez le répertoire hôte (si nécessaire)
sudo mkdir -p /opt/itadmin

# Copiez sample.txt depuis /tmp vers /opt/itadmin
sudo cp /tmp/sample.txt /opt/itadmin/

# Vérifiez que le fichier est présent
ls -l /opt/itadmin/sample.txt
```

2) Récupérer l'image `nginx` (tag `latest` recommandé)

```bash
# Optionnel : mettre à jour la liste d'images locales
sudo docker pull nginx:latest

# (Si vous préférez un autre tag)
# sudo docker pull nginx:1.25-alpine
```

3) Démarrer le conteneur nommé `media` avec le volume monté et en arrière-plan

```bash
sudo docker run -d \
  --name media \
  -v /opt/itadmin:/home \
  nginx:latest

# Explication des options :
# -d           : détaché (container en arrière-plan et en état running)
# --name media : nomme le conteneur "media"
# -v /opt/itadmin:/home : monte le répertoire hôte dans /home du conteneur
```

4) Vérifications

```bash
# Voir les conteneurs en cours
sudo docker ps --filter "name=media"

# Inspecter le contenu de /home dans le conteneur
sudo docker exec -it media ls -la /home

# Afficher les logs du container (si besoin)
sudo docker logs media

# Tester l'accès HTTP depuis le serveur hôte (nginx écoute par défaut sur 80)
# Installez curl si nécessaire : sudo apt-get install -y curl
curl -I http://localhost
```

5) Commandes utiles pour arrêter / supprimer

```bash
# Arrêter
sudo docker stop media

# Supprimer
sudo docker rm media

# Supprimer l'image locale (si besoin)
sudo docker rmi nginx:latest
```

Remarques pédagogiques :
- Le montage `-v /opt/itadmin:/home` rend le contenu de `/opt/itadmin` accessible dans le conteneur à l'emplacement `/home`.
- Assurez-vous que les permissions sur `/opt/itadmin/sample.txt` permettent la lecture par le processus nginx (généralement root suffit pour tests locaux).
- L'option `-d` démarre le conteneur en arrière-plan : il restera en `running` jusqu'à arrêt manuel ou erreur.
- Si SELinux est activé, vous pourriez avoir besoin d'options supplémentaires (`:Z` ou `:z`) sur le montage : `-v /opt/itadmin:/home:Z`.

Exemple complet en bloc (copier-coller)

```bash
sudo mkdir -p /opt/itadmin
sudo cp /tmp/sample.txt /opt/itadmin/
sudo docker pull nginx:latest
sudo docker run -d --name media -v /opt/itadmin:/home nginx:latest
sudo docker ps --filter "name=media"
sudo docker exec -it media ls -la /home
```

Besoin d'aide ?
- Si vous voulez, je peux :
  - Fournir un fichier `docker-compose.yml` équivalent pour l'exercice.
  - Adapter les instructions pour une configuration non privilégiée (utilisateur docker non-root).
  - Ajouter des tests automatisés simples pour vérifier le montage de volume.

Bonne pratique : Conservez ce README pour la formation et exécutez les commandes pas à pas pour bien comprendre chaque étape.
