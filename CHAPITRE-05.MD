### Chapitre-05 : Scaling et Optimisation des Applications Node.js

Ce cours combine les concepts de scaling et d'optimisation des applications Node.js en utilisant des techniques comme le clustering, la mise en cache avec Redis, et la scalabilité via les microservices et le load balancing. Nous allons également explorer des outils comme PM2 pour la gestion des processus.

---

### **Introduction**

Node.js est un environnement d'exécution JavaScript basé sur un modèle à un seul thread, ce qui le rend très performant pour les applications I/O-bound (comme les serveurs web). Cependant, pour les applications CPU-bound ou celles qui doivent gérer un grand nombre de requêtes simultanées, il est essentiel de tirer parti des ressources multi-cœurs disponibles sur les machines modernes. Ce cours vous montrera comment optimiser et scaler vos applications Node.js en utilisant des techniques avancées.

---

### **1. Scaling avec le Clustering**

#### **Problématique**
Node.js, par défaut, utilise un seul thread pour exécuter votre code JavaScript. Cela signifie que si votre application reçoit un grand nombre de requêtes, un seul CPU sera sollicité, ce qui peut entraîner des goulots d'étranglement et une dégradation des performances.

#### **Solution : Le Module Cluster**
Le module `cluster` de Node.js permet de créer plusieurs instances de votre application (appelées "workers") qui fonctionnent en parallèle sur différents cœurs de CPU. Cela permet de répartir la charge entre plusieurs processus, améliorant ainsi les performances et la disponibilité de l'application.

#### **Exemple de Code : Clustering avec Node.js**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');
const numCPUs = os.cpus().length;

if (cluster.isMaster) {
  console.log(`Le processus maître est ${process.pid}`);
  
  // Création d'un worker pour chaque cœur de CPU
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Gestion de la mort d'un worker
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Le worker ${worker.process.pid} est mort. Redémarrage...`);
    cluster.fork(); // Redémarre un nouveau worker
  });
} else {
  // Chaque worker crée un serveur HTTP
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hello, World! Depuis le worker ${process.pid}`);
  }).listen(8000, () => {
    console.log(`Worker ${process.pid} en écoute sur le port 8000`);
  });
}
```

#### **Explication**
- **`cluster.isMaster`** : Vérifie si le processus actuel est le processus principal (master). Si c'est le cas, il crée des workers.
- **`cluster.fork()`** : Crée un nouveau worker pour chaque cœur de CPU.
- **`cluster.on('exit')`** : Écoute les événements de sortie des workers et redémarre un nouveau worker si l'un d'eux meurt.
- **Serveur HTTP** : Chaque worker exécute un serveur HTTP indépendant, permettant de répartir la charge entre les cœurs CPU.

---

### **2. Mise en Cache avec Redis**

#### **Problématique**
Les applications Node.js peuvent être ralenties par des requêtes répétitives vers une base de données ou des calculs coûteux. La mise en cache permet de stocker les résultats de ces opérations pour les réutiliser, réduisant ainsi la latence et la charge sur le serveur.

#### **Solution : Redis**
Redis est une base de données en mémoire, idéale pour la mise en cache. Elle permet de stocker des données fréquemment demandées et de les récupérer rapidement.

#### **Exemple de Code : Mise en Cache avec Redis**

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

#### **Explication**
- **`client.get(cacheKey)`** : Vérifie si les données sont déjà en cache.
- **`client.setex(cacheKey, 3600, dataFromDB)`** : Stocke les données en cache pendant 1 heure (3600 secondes).
- **`fetchDataFromDB`** : Simule une requête à la base de données avec un délai de 2 secondes.

---

### **3. Scalabilité via Microservices et Load Balancing**

#### **Microservices**
Les microservices consistent à diviser une application en plusieurs petits services indépendants, chacun ayant une fonctionnalité spécifique. Cela permet de déployer, maintenir et scaler chaque service indépendamment.

#### **Load Balancing avec Nginx**
Pour répartir la charge entre plusieurs instances de votre application, vous pouvez utiliser Nginx comme reverse proxy.

#### **Exemple de Configuration Nginx pour le Load Balancing**

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

#### **Explication**
- **`upstream`** : Définit un groupe de serveurs (ici deux instances de Node.js sur les ports 3000 et 3001).
- **`proxy_pass`** : Redirige les requêtes HTTP vers le groupe de serveurs spécifié.

---

### **4. Utilisation de PM2 pour la Gestion des Processus**

#### **Problématique**
La gestion manuelle des processus Node.js peut être fastidieuse, surtout lorsque vous devez redémarrer des workers ou surveiller les performances.

#### **Solution : PM2**
PM2 est un gestionnaire de processus pour Node.js qui permet de gérer facilement les clusters, surveiller les performances, et redémarrer automatiquement les processus en cas de crash.

#### **Exemple de Code : Utilisation de PM2**

```bash
# Démarrer un cluster avec PM2
pm2 start index.js -i 0

# Voir les logs des processus
pm2 logs

# Voir l'état des processus
pm2 ls

# Redémarrer le cluster
pm2 restart cluster_app

# Arrêter le cluster
pm2 stop cluster_app

# Supprimer le cluster
pm2 delete cluster_app
```

#### **Explication**
- **`pm2 start index.js -i 0`** : Démarre un cluster avec autant de workers que de cœurs CPU disponibles.
- **`pm2 logs`** : Affiche les logs des processus.
- **`pm2 ls`** : Affiche l'état des processus en cours d'exécution.

---

### **5. TP : Optimisation de votre Application**

#### **Étapes du TP**
1. **Clustering avec Node.js** :
   - Créez une application Node.js simple et implémentez le clustering pour répartir les requêtes HTTP entre plusieurs workers.
   - Testez les performances avant et après l'implémentation du clustering.

2. **Mise en Cache avec Redis** :
   - Ajoutez un mécanisme de cache avec Redis pour mettre en cache les réponses de l'API.
   - Comparez les temps de réponse avant et après la mise en cache.

3. **Scalabilité via Microservices** :
   - Divisez votre application en microservices (par exemple, un service pour les utilisateurs et un autre pour les produits).
   - Déployez chaque microservice indépendamment.

4. **Répartition de Charge avec Nginx** :
   - Déployez deux instances de votre application Node.js sur des ports différents.
   - Configurez Nginx pour répartir le trafic entre ces deux instances.

---

### **Ce qu'il faut retenir**

En combinant ces techniques (clustering, mise en cache, microservices, et load balancing), vous pouvez considérablement améliorer les performances et la scalabilité de vos applications Node.js. Ces outils et méthodes vous permettront de gérer efficacement les charges élevées et d'optimiser l'utilisation des ressources de votre serveur.
