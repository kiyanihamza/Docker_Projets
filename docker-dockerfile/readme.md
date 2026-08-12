Déploiement d'un site statique sur httpd (exercice)

## Objectif
Déployer le contenu statique fourni par l'équipe Nautilus dans un conteneur `httpd` sur App Server 3 (Stratos DC) en utilisant Docker Compose. Ce README fournit les instructions et le fichier `docker-compose.yml` recommandé.

> Important : ne pas modifier les données situées dans `/opt/data` (montage en lecture seule recommandé).

## Prérequis
- Accès SSH à App Server 3 avec privilèges `sudo`.
- Docker et Docker Compose installés (`docker` ou plugin `docker compose`).
- Le répertoire `/opt/data` existe et contient le site statique à servir.
- Le fichier de compose doit se trouver exactement à `/opt/docker/docker-compose.yml`.

## Contenu recommandé pour `/opt/docker/docker-compose.yml`
Placez ce contenu dans le fichier exactement à l'emplacement `/opt/docker/docker-compose.yml` :

```yaml
version: "3.8"

services:
	web:
		image: httpd:latest
		container_name: httpd
		ports:
			- "5001:80"
		
		restart: unless-stopped
```

- L'image utilisée est `httpd:latest`.
- Le conteneur porte le nom exact `httpd` (`container_name: httpd`).
- Le service peut avoir n'importe quel nom (ici `web`).
- Le port hôte `5001` est mappé au port `80` du conteneur.
- Le volume est monté en lecture seule (`:ro`) pour éviter toute modification accidentelle.

## Commandes (exemples)

Créer le répertoire et écrire le fichier (copier-coller le YAML ci-dessus) :

```bash
sudo mkdir -p /opt/docker
sudo tee /opt/docker/docker-compose.yml > /dev/null <<'EOF'
# (coller le contenu YAML ici)
EOF
```

Démarrer le service :

```bash
# Avec le plugin docker compose (v2)
sudo docker compose -f /opt/docker/docker-compose.yml up -d

# Ou avec l'ancien binaire
sudo docker-compose -f /opt/docker/docker-compose.yml up -d
```

Vérifications :

```bash
sudo docker ps --filter "name=httpd"
sudo docker logs httpd --tail 50
curl -I http://localhost:5001
```

Arrêt / nettoyage :

```bash
sudo docker compose -f /opt/docker/docker-compose.yml down
# ou
sudo docker-compose -f /opt/docker/docker-compose.yml down
```

## Sécurité et bonnes pratiques
- Monter `/opt/data` en lecture seule (`:ro`) prévient les écritures accidentelles.
- Vérifier les règles de pare-feu (port `5001`) et SELinux si applicable.
- Ne pas modifier les fichiers sous `/opt/data` sans accord préalable.

---

Fichier fourni : [readme.md](readme.md)

