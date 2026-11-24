# TP4 : Docker et Docker Compose - Application MERN


---

## 1. Structure du projet

```
MyAppReact/
│
├── Client/
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── Dockerfile
│   └── .dockerignore
│
├── Server/
│   ├── src/
│   ├── package.json
│   ├── Dockerfile
│   └── .dockerignore
│
├── docker-compose.yml
├── Jenkinsfile
└── README.md
```

---

## 2. Instructions pas à pas réalisées

### Étape 1 : Cloner le projet
Le projet a été cloné depuis :
```bash
git clone https://github.com/wiem-kb/MyAppReact.git
```

### Étape 2 : Créer les Dockerfiles

#### a. Pour le serveur (Express) :
Fichier : `Server/Dockerfile`
- Image de base : `node:lts-alpine`
- Répertoire de travail : `/app`
- Copie de `package*.json`
- Installation des dépendances avec `npm install`
- Copie des sources du serveur
- Exposition du port `9000`
- Commande de démarrage : `npm start`
- Commande pour construire manuellement l'image :
  ```bash
  docker build -t mern-server ./Server
  ```

#### b. Pour le client (React) :
Fichier : `Client/Dockerfile`
 Image de base : `node:lts-alpine`
 Répertoire de travail : `/app`
 Copie de `package*.json`
 Installation des dépendances avec `npm install`
 Copie des fichiers du client
 Construction de l'application avec `npm run build`
 Installation d'un serveur HTTP léger (`serve`) via `npm install -g serve`
 Exposition du port `3000`
Démarrage du serveur pour servir les fichiers statiques de build (`serve -s dist -l 3000`)
 Commande pour construire manuellement l'image :
  ```bash
  docker build -t mern-client ./Client
  ```

---

### Étape 3 : Créer un réseau Docker (Mode manuel)
Pour permettre la communication directe entre les conteneurs :
```bash
docker network create mern-network
```

---

### Étape 4 : Exécuter MongoDB dans un conteneur (Mode manuel)
```bash
docker run -d --name mongodb --network mern-network mongo
```

---

### Étape 5 : Exécuter les conteneurs du serveur et du client (Mode manuel)

- **Serveur :**
  ```bash
  docker run -d --name server --network mern-network -p 9000:9000 mern-server
  ```

- **Client :**
  ```bash
  docker run -d --name client --network mern-network -p 3000:3000 mern-client
  ```

---

### Étape 6 : Orchestration avec Docker Compose
Le fichier `docker-compose.yml` situé à la racine automatise l'ensemble de ces étapes :
- **mongodb** : Image `mongo`, conteneur nommé `mongodb`, réseau `mern-network`.
- **server** : Construit à partir de `./Server`, conteneur nommé `server`, port mappé `9000:9000`, réseau `mern-network`, dépendance `depends_on: [mongodb]`, variable d'environnement `MONGO_URI=mongodb://mongodb:27017/mern`.
- **client** : Construit à partir de `./Client`, conteneur nommé `client`, port mappé `3000:3000`, réseau `mern-network`, dépendance `depends_on: [server]`.
- **Réseau** : `mern-network` configuré avec le driver `bridge`.

#### Commande pour lancer toute l'application :
```bash
docker compose up --build
```

Pour arrêter les conteneurs :
```bash
docker compose down
```

---

## 3.  pour Automatisation CI/CD avec Jenkins Jenkinsfile


1. ### Checkout** : Récupération du code source depuis le dépôt Git.
2. ### Build Docker Images: Construction des images (`docker compose build`).
3. ## Deploy / Run Application : Lancement des conteneurs en arrière-plan (`docker compose up -d`).
4. ## Health Check : Vérification du statut des services (`docker compose ps`).
5. ## Post-actions : Affichage des logs en cas d'échec pour un diagnostic immédiat.


