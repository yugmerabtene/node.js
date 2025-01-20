### Chapitre 05 : Optimisation des performances pour les applications Node.js

#### 1. **Utilisation de Clusters**

Node.js fonctionne sur un seul thread, mais en exploitant les **clusters**, vous pouvez créer plusieurs processus enfants, chacun s'exécutant sur un cœur différent du processeur.

**Exemple de code :**

```javascript
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  // Création d'un worker pour chaque cœur du CPU
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`Le processus worker ${worker.process.pid} a terminé`);
  });
} else {
  // Serveur HTTP dans chaque worker
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('Hello, World!');
  }).listen(8000);
}
```

Dans cet exemple, chaque cœur du processeur exécute un processus enfant qui répond aux requêtes HTTP, améliorant ainsi la gestion des connexions simultanées.

#### 2. **Mise en cache avec Redis**

Redis est une base de données en mémoire très rapide qui peut être utilisée pour stocker des données fréquemment demandées, réduisant ainsi les requêtes répétitives vers la base de données principale.

**Exemple de code pour mettre en cache une réponse avec Redis :**

```javascript
const redis = require('redis');
const client = redis.createClient();
const express = require('express');
const app = express();

// Exemple de fonction qui récupère des données de la base
function fetchDataFromDB(callback) {
  // Simulons une lecture de base de données qui prend du temps
  setTimeout(() => {
    callback("Données récupérées de la base de données");
  }, 2000);
}

app.get('/data', (req, res) => {
  const cacheKey = 'my_data_cache';
  
  // Vérifier si les données sont en cache
  client.get(cacheKey, (err, data) => {
    if (data) {
      return res.send(data); // Utilise les données en cache
    }
    
    // Si pas en cache, récupérer depuis la base et mettre en cache
    fetchDataFromDB((dataFromDB) => {
      client.setex(cacheKey, 3600, dataFromDB); // Mise en cache pour 1 heure
      res.send(dataFromDB);
    });
  });
});

app.listen(3000, () => {
  console.log('Serveur en écoute sur le port 3000');
});
```

Dans cet exemple, Redis est utilisé pour mettre en cache les réponses de la base de données pendant une heure. Si les données sont déjà présentes en cache, elles sont directement retournées sans accéder à la base de données.

#### 3. **Techniques avancées de scalabilité**

##### **Microservices**

En divisant une application en petits services indépendants, chaque service peut évoluer et se scaler de manière autonome.

**Exemple :**
- **Service A** : Responsable de l'authentification
- **Service B** : Responsable des données utilisateurs
- **Service C** : Gère les transactions

Chaque service peut être déployé et scalé indépendamment pour mieux gérer les pics de charge.

##### **Répartition de charge (Load balancing)**

Un répartiteur de charge distribue les requêtes entre plusieurs instances de votre application Node.js, améliorant ainsi la gestion des requêtes simultanées.

**Exemple de load balancing avec Nginx :**

```nginx
http {
    upstream node_app {
        server 127.0.0.1:3000;
        server 127.0.0.1:3001;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://node_app;
        }
    }
}
```

Nginx agit ici comme un répartiteur de charge, distribuant les requêtes entre les serveurs Node.js qui tournent sur les ports 3000 et 3001.

#### 4. **TP : Optimisation de votre application**

1. **Clustering avec Node.js** :
   - Implémentez un système de clustering dans votre propre application Node.js pour exploiter tous les cœurs du CPU.
   
2. **Mise en cache avec Redis** :
   - Ajoutez Redis à votre application pour mettre en cache des résultats de requêtes fréquentes et comparer les temps de réponse avec et sans cache.

3. **Scalabilité via microservices** :
   - Divisez une partie de votre application en microservices, puis déployez et testez ces services de manière indépendante.

4. **Répartition de charge** :
   - Utilisez un serveur Nginx pour répartir le trafic HTTP entre plusieurs instances de votre application Node.js.
