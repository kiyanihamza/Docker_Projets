# Exercice Docker : Création d'un réseau macvlan

## Objectif
Cet exercice montre comment créer un réseau Docker spécialisé pour des services applicatifs qui nécessitent des adresses IP sur un sous-réseau dédié.

## Contexte
- Nom du réseau : `news`
- Environnement : App Server `1` dans le data center `Stratos DC`
- Pilote réseau : `macvlan`
- Sous-réseau : `10.10.1.0/24`
- Plage d'adresses IP : `10.10.1.0/24`

## Commande Docker
```bash
docker network create \
  --driver macvlan \
  --subnet 10.10.1.0/24 \
  --ip-range 10.10.1.0/24 \
  --gateway 10.10.1.1 \
  --opt parent=eth0 \
  news
```

> Remarque : `--opt parent=eth0` doit correspondre à l'interface réseau physique ou virtuelle du serveur Docker. Sur votre serveur réel, remplacez `eth0` par l'interface appropriée (`ens18`, `enp0s3`, etc.).

## Explications
- `docker network create` : crée un réseau Docker.
- `--driver macvlan` : utilise le pilote `macvlan`, qui permet aux conteneurs d'apparaître directement sur le réseau local avec des adresses MAC/IP distinctes.
- `--subnet 10.10.1.0/24` : définit le bloc d'adresses IP du réseau.
- `--ip-range 10.10.1.0/24` : réserve toutes les adresses de ce sous-réseau pour Docker.
- `--gateway 10.10.1.1` : définit la passerelle du réseau (optionnel mais recommandé).

## Compléments pédagogiques
1. Vérifier la création du réseau :
   ```bash
   docker network ls
   docker network inspect news
   ```
2. Lancer un conteneur sur ce réseau :
   ```bash
   docker run -d --name test-news --network news alpine sleep 3600
   ```
3. Testez la connectivité IP entre conteneurs si le réseau physique le permet.

## Utilisation
Ce réseau pourra être utilisé plus tard pour des applications qui nécessitent des adresses IP fixes dans le DC `Stratos`.
