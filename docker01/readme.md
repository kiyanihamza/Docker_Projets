# Docker Setup for App Server 2

## Objectif

Préparer App Server 2 pour exécuter des conteneurs Docker et tester la containerisation des applications.

## Étapes d’installation

### 1. Se connecter au serveur

```bash
ssh <user>@<app-server-2-ip>
```

### 2. Mettre à jour les paquets du système

```bash
sudo dnf update -y
```

### 3. Installer les dépendances nécessaires

```bash
sudo dnf install -y yum-utils device-mapper-persistent-data lvm2
```

### 4. Ajouter le dépôt Docker

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 5. Installer Docker CE et Docker Compose

Sur CentOS, l’installation se fait généralement avec DNF :

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Si votre environnement ne fournit pas le plugin Docker Compose, vous pouvez installer la version classique :

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-compose
```

### 6. Démarrer et activer le service Docker

```bash
sudo systemctl enable --now docker
```

## Vérification

Vérifiez que Docker est bien installé et en cours d’exécution :

```bash
docker --version
docker compose version
sudo systemctl status docker
```

## Résultat attendu

- Docker CE est installé sur App Server 2.
- Le service Docker est actif.
- La commande `docker compose version` fonctionne correctement.

## Notes

- Si vous rencontrez une erreur liée au dépôt, vérifier l’URL du dépôt ou la distribution utilisée.
- Les permissions peuvent nécessiter l’utilisation de `sudo` ou l’ajout de l’utilisateur au groupe `docker`.
- Sur CentOS, `dnf` est la commande recommandée pour l’installation des paquets système.
