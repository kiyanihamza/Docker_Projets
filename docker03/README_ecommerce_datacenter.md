# Scénario : Créer une image `ecommerce:datacenter` depuis un conteneur

Ce document décrit comment, depuis **Application Server 2**, créer une image Docker nommée `ecommerce:datacenter` à partir d'un conteneur en cours d'exécution nommé `ubuntu_latest`.

## Contexte
- Un développeur a modifié des fichiers dans le conteneur `ubuntu_latest` sur Application Server 2.
- L'équipe DevOps doit produire une image Docker `ecommerce:datacenter` contenant ces modifications.

## Prérequis
- Accès SSH à Application Server 2 (compte avec droits Docker).
- Docker installé et opérationnel sur Application Server 2.
- Connaître le nom ou l'ID du conteneur (ici `ubuntu_latest`).

## Rappel important
`docker commit` capture l'état courant du système de fichiers du conteneur (y compris fichiers modifiés, paquets installés, etc.). Ce procédé est utile pour sauvegarder un état de travail, mais n'est pas reproductible comme un Dockerfile. Pour la production, transposer les changements dans un `Dockerfile` reste recommandé.

## Étapes (commande par commande)

1) Se connecter sur Application Server 2

```bash
ssh <user>@application-server-2
```

2) Vérifier le conteneur en cours d'exécution et identifier `ubuntu_latest`

```bash
docker ps -a --filter "name=ubuntu_latest"
# ou
docker ps -a
```

3) (Optionnel) Arrêter des services propres à l'application dans le conteneur

# Si besoin, exécuter une commande dans le conteneur pour arrêter proprement les services
```bash
docker exec -it ubuntu_latest bash -lc "service myapp stop || true"
```

4) Créer l'image depuis le conteneur

```bash
docker commit ubuntu_latest ecommerce:datacenter
```

Notes:
- `ubuntu_latest` peut être remplacé par l'ID du conteneur (ex: `a1b2c3d4e5f6`).
- L'image locale résultante porte le nom `ecommerce` avec le tag `datacenter`.

5) Vérifier que l'image a été créée

```bash
docker images | grep ecommerce
docker inspect ecommerce:datacenter
```

6) Exporter l'image vers un fichier tar (optionnel, pour transfert manuel)

```bash
docker save -o ecommerce_datacenter.tar ecommerce:datacenter
```

7) Pousser l'image vers un registre distant (optionnel — recommandé en production)

```bash
# Tagger pour le registre (exemple `registry.example.com`)
docker tag ecommerce:datacenter registry.example.com/myteam/ecommerce:datacenter
docker push registry.example.com/myteam/ecommerce:datacenter
```

8) Charger l'image sur un autre hôte (si vous avez exporté le tar)

```bash
scp ecommerce_datacenter.tar other-host:/tmp/
ssh other-host
docker load -i /tmp/ecommerce_datacenter.tar
```

## Vérifications post-création
- Lancer un conteneur depuis la nouvelle image et valider le comportement.

```bash
docker run --rm -d --name test_ecom ecommerce:datacenter
docker logs -f test_ecom
docker exec -it test_ecom bash
```

## Bonnes pratiques et recommandations
- Préparer un `Dockerfile` et reconstruire l'image à partir du code source pour rendre l'image reproductible.
- Utiliser un registre interne (ou Docker Hub privé) pour versionner et partager l'image.
- Documenter les changements appliqués dans le conteneur (quel code, quelles modifications, quelles commandes ont été exécutées) afin que l'image puisse être auditée.

## Exemple résumé (script rapide)

```bash
ssh <user>@application-server-2 <<'SSH'
docker ps -a --filter "name=ubuntu_latest"
docker commit ubuntu_latest ecommerce:datacenter
docker images | grep ecommerce
SSH
```

---

Si vous le souhaitez, je peux :
- générer un `Dockerfile` de base pour rendre l'image reproductible,
- ou préparer les commandes pour pousser l'image vers un registre (indiquer l'URL et les identifiants du registre si nécessaires).
