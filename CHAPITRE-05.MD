### Chapitre 05 : Optimisation des performances pour les applications Node.js

#### 1. **Utilisation de Clusters**

Node.js est basé sur un modèle à un seul thread, ce qui limite son utilisation des ressources multi-cœurs. Cependant, avec l'utilisation des **clusters**, vous pouvez créer plusieurs processus enfants, chaque processus étant exécuté sur un cœur du processeur. Cela permet de tirer parti des capacités de traitement parallèles du système.



##### **Exemple complet de code (Cluster avec Node.js)**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');
const numCPUs = os.cpus().length;

if (cluster.isMaster) {
  // Création d'un processus worker pour chaque cœur du CPU
  console.log(`Le processus maître est ${process.pid}`);
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
    res.end(`Hello, World! Depuis le worker ${process.pid}`);
  }).listen(8000, () => {
    console.log(`Worker ${process.pid} en écoute sur le port 8000`);
  });
}
```

##### **Explication détaillée**:
- **`cluster.isMaster`** : Si le processus est le processus principal (master), il crée autant de processus enfants (workers) qu'il y a de cœurs CPU. Ces workers gèrent les requêtes HTTP.
- **`cluster.fork()`** : Crée un worker pour chaque cœur du processeur.
- **`cluster.on('exit')`** : Écoute les événements de sortie des processus workers.
- **Le code dans la partie `else`** crée un serveur HTTP dans chaque worker. Chaque worker répond aux requêtes HTTP sur le même port.

#### 2. **Mise en cache avec Redis**

Redis est une solution de mise en cache en mémoire, idéale pour stocker des données fréquemment demandées et éviter de solliciter la base de données à chaque requête.

##### **Exemple de code pour mettre en cache avec Redis :**

```javascript
const redis = require('redis');
const client = redis.createClient();
const express = require('express');
const app = express();

// Simuler une requête à la base de données
function fetchDataFromDB(callback) {
  setTimeout(() => {
    callback("Données récupérées de la base de données");
  }, 2000);
}

app.get('/data', (req, res) => {
  const cacheKey = 'my_data_cache';
  
  // Vérifier si les données sont déjà en cache
  client.get(cacheKey, (err, data) => {
    if (data) {
      return res.send(data); // Renvoie les données depuis le cache
    }
    
    // Sinon, récupère les données depuis la base de données et les met en cache
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

##### **Explication détaillée**:
- **Redis** est utilisé pour vérifier si les données sont présentes en cache.
- Si les données sont présentes en cache (`client.get(cacheKey)`), elles sont retournées immédiatement.
- Sinon, les données sont récupérées depuis la base de données avec un délai simulé et ensuite mises en cache pendant une heure avec `client.setex()`.

#### 3. **Scalabilité via Microservices et Load Balancing**

##### **Microservices**
Les microservices consistent à découper l'application en petites unités indépendantes qui communiquent entre elles via des API. Chaque microservice peut être déployé, maintenu et scalé indépendamment.

##### **Répartition de charge avec Nginx**
Pour répartir la charge entre plusieurs instances de votre application, vous pouvez utiliser un serveur Nginx comme reverse proxy.

##### **Exemple de configuration Nginx pour le Load Balancing :**

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

##### **Explication détaillée**:
- **`upstream`** : Déclare un groupe de serveurs (ici deux instances de Node.js en local sur les ports 3000 et 3001).
- **`proxy_pass`** : Redirige les requêtes HTTP entrantes vers le groupe de serveurs spécifié.

#### 4. **TP : Optimisation de votre application**

Pour appliquer ces concepts, voici les étapes du TP à suivre :

1. **Clustering avec Node.js** :
   - Créez une application Node.js qui utilise le clustering pour répartir les requêtes HTTP entre plusieurs processus enfants, chacun exécutant un serveur HTTP. 
   - Testez les performances de l'application avant et après l'implémentation du clustering.

2. **Mise en cache avec Redis** :
   - Ajoutez un mécanisme de cache avec Redis dans votre application pour mettre en cache les réponses de l'API ou des requêtes longues.
   - Testez les temps de réponse avant et après la mise en cache.

3. **Scalabilité via Microservices** :
   - Divisez une partie de votre application en microservices. Par exemple, créez un service pour la gestion des utilisateurs et un autre pour la gestion des produits.
   - Déployez les microservices et assurez-vous qu'ils fonctionnent indépendamment.

4. **Répartition de charge avec Nginx** :
   - Déployez deux instances de votre application Node.js sur des ports différents.
   - Utilisez Nginx pour répartir le trafic entre ces deux instances et observez l'amélioration de la gestion des requêtes simultanées.
