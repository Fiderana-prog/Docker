# Examen Docker  
__RANARISON ANDRIANJAFY Fiderana Yves Denis__  
*L1A/154/24-25*  

## Généralités sur Docker

**Docker** est un outil qui a permis de populariser la conteneurisation, un mode de mutualisation ou de virtualisation des serveurs.

### 1. Objectifs
- Simplifier les déploiements  
- Changer le mode de livraison  
- Faciliter la gestion des dépendances  

### 2. Concepts ou principes

**Image** : Un élément inactif contenant :
- Le code du ou des programmes  
- Les dépendances nécessaires  

**Conteneur** : Activation d'une image, avec :
- Un ou plusieurs processus  
- Une isolation via cgroups / namespaces  

## Les commandes basiques sur Docker

---

```bash
sudo usermod -aG docker $USER
```
Ajoute l'utilisateur au groupe docker.

---

```bash
docker ps
```
Affiche les conteneurs en cours d'exécution.

---

```bash
docker ps -a
```
Liste tous les conteneurs, actifs ou non.

---

```bash
docker ps -q
```
Liste uniquement les IDs des conteneurs actifs.

---

```bash
docker ps -qa
```
Liste uniquement les IDs de tous les conteneurs.

---

```bash
docker run nginx:latest
```
Lance un conteneur Nginx.

---

```bash
docker run -d nginx:latest
```
Lance Nginx en arrière-plan.

---

```bash
docker run -d --name c1 nginx:latest
```
Lance Nginx dans un conteneur nommé `c1`.

---

```bash
docker rm -f c1
```
Supprime le conteneur `c1` même s'il tourne.

---

```bash
docker run -ti --name c1 debian:latest
```
Lance un conteneur Debian interactif nommé `c1`.

---

```bash
docker run -ti --rm --name c1 debian:latest
```
Lance un conteneur temporaire interactif.

---

## Docker Volume

Un volume Docker est un espace de stockage persistant.

### Intérêts :
- Persistance  
- Backups  
- Partage inter-conteneurs  
- Gestion des permissions  
- Local ou distant  

---

```bash
docker volume ls
```
Liste les volumes disponibles.

---

```bash
docker volume create mynginx
```
Crée un volume `mynginx`.

---

```bash
docker run -d --name c1 -v mynginx:/usr/share/nginx/html/ nginx:latest
```
Monte le volume dans le conteneur.

---

```bash
docker exec -ti c1 bash
```
Accède à un terminal dans `c1`.

---

```bash
docker volume inspect mynginx
```
Affiche les détails du volume.

---

## Types de Docker Volumes

### Bind Mount

Montage d’un dossier de l’hôte dans un conteneur.

```bash
sudo mkdir /data  
sudo mkdir /data2  
sudo touch /data/Hello  
sudo mount --bind /data/ /data2  
sudo findmnt
```

- `/data2` devient un miroir de `/data`.

### Différences :

- **Bind Mount** : les données de l’hôte écrasent celles de l’image.  
- **Docker Volume** : les données de l’image remplissent le volume.

### Exemple :

**Bind Mount**

```bash
docker run -d --name c1 --mount type=bind,source=/data/,destination=/usr/share/nginx/html/ nginx:latest
```

**Docker Volume**

```bash
docker run -d --name c2 --mount type=volume,source=mynginx,destination=/usr/share/nginx/html/ nginx:latest
```

---

## Docker Networks

Les réseaux Docker permettent la communication entre conteneurs.

### 1. Bridge

Réseau par défaut, isolé.

```bash
docker run --name c1 -d debian sleep infinity
docker exec -ti c1 bash
apt install iputils-ping net-tools
ifconfig
```

### 2. Réseau personnalisé

```bash
docker network ls
```

Liste les réseaux existants.

---

```bash
docker network create --driver=bridge --subnet=192.168.0.0/24 réseau1
```
Crée un réseau bridge personnalisé.

---

```bash
docker network inspect réseau1
```
Affiche les détails du réseau.

---

```bash
docker run -d --name c1 --network réseau1 nginx:latest
docker run -d --name c2 --network réseau1 nginx:latest
```

---

```bash
docker exec -ti c2 bash
apt install iputils-ping
ping c1
```

Résolution DNS : `c1.réseau1`

---

## Dockerfile

Fichier de création d’images Docker personnalisées.

### Contenu :
- Image de base (`FROM`)
- Instructions (`RUN`, `COPY`, etc.)
- Variables (`ENV`, `ARG`)
- Lancement (`CMD`, `ENTRYPOINT`)
- Autres (`LABEL`, `EXPOSE`, `VOLUME`, `USER`, etc.)

---

### Commandes Dockerfile

```bash
docker build -t myimage .
```

Construit l'image `myimage`.

---

```bash
docker images
```

Liste les images locales.

---

```bash
docker run -d -p 8080:80 myimage
```

Lance le conteneur sur le port 8080.

---

```bash
docker run -d --name c1 myimage
```

Lance `c1` à partir de `myimage`.

---

```bash
docker rmi -f myimage
```

Supprime l’image même utilisée.

---

## Docker Layers

Chaque instruction dans le Dockerfile crée une **layer**.

### Avantages :
- Cache de build  
- Optimisation de l’espace disque  
- Isolation et empilement de couches  
