### Projet : Clone de Twitter

**Descriptif Technique :**
- **Backend** : Node.js avec gestion du multithreading pour optimiser les requêtes concurrentes.
- **Base de données** :
  - **Redis** : utilisé comme cache pour les données fréquemment consultées.
  - **MySQL** : base de données principale avec un nombre réduit de tables, pour stocker les informations persistantes.
- **Logique** :
  - Si une donnée est présente dans Redis, elle est récupérée directement depuis le cache.
  - Si la donnée n'est pas dans Redis, elle est récupérée depuis MySQL, affichée et ensuite stockée dans Redis pour les requêtes futures.

- **Frontend** :
  - **React.js** : utilisé pour l'interface utilisateur.
  - **HTML/CSS** : pour la structure et le design de la page.
  
- **Fonctionnalité** :
  - Un utilisateur peut choisir un pseudonyme sans avoir à créer de compte.
  - Il peut publier des messages sur un mur commun.

- **Tests** :
  - Utilisation de Jest pour l'écriture des tests unitaires.

### User Stories :

1. **En tant qu'utilisateur**, je souhaite pouvoir choisir un pseudonyme afin de publier sur le mur commun sans avoir besoin de créer un compte.
   - **Critère d'acceptation** : L'utilisateur peut entrer un pseudonyme unique sur l'interface sans créer de compte. Le pseudonyme est validé et stocké en mémoire pendant la session.

2. **En tant qu'utilisateur**, je souhaite pouvoir publier un message sur le mur commun pour partager des informations.
   - **Critère d'acceptation** : L'utilisateur peut saisir un message et cliquer sur un bouton pour le publier. Le message est ajouté au mur commun et visible pour tous les autres utilisateurs.

3. **En tant qu'utilisateur**, je veux que mes publications soient visibles immédiatement sur le mur, même si un autre utilisateur vient de publier un message.
   - **Critère d'acceptation** : Dès qu'une publication est effectuée, elle est immédiatement visible sur le mur commun sans rechargement de la page.

4. **En tant que système**, je veux utiliser Redis comme cache afin d'accélérer l'affichage des messages du mur commun.
   - **Critère d'acceptation** : Les messages doivent être récupérés à partir de Redis lorsqu'ils sont déjà en cache, pour des temps de réponse rapides.

5. **En tant que système**, je veux que les messages soient persistants dans la base de données MySQL pour éviter toute perte de données en cas de redémarrage du serveur.
   - **Critère d'acceptation** : Si les messages ne sont pas dans Redis, ils sont récupérés depuis MySQL et affichés. Après récupération, les messages sont stockés dans Redis pour une utilisation future.

6. **En tant qu'utilisateur**, je veux que le système affiche correctement les données du mur même si Redis n'est pas disponible, en basculant sur MySQL.
   - **Critère d'acceptation** : Si Redis n'est pas disponible, les messages doivent être récupérés depuis MySQL sans affecter l'expérience de l'utilisateur.

7. **En tant que développeur**, je veux que le système de publication soit testé automatiquement pour garantir son bon fonctionnement.
   - **Critère d'acceptation** : Des tests unitaires sont écrits en utilisant Jest pour valider que la publication des messages fonctionne comme prévu.

### Tests avec Jest :
- **Test de publication** : Vérifier que lorsqu'un utilisateur publie un message, il est correctement ajouté au mur.
- **Test de récupération de données** : Vérifier que les données sont d'abord récupérées depuis Redis et, en cas d'absence, depuis MySQL.
- **Test de performance** : Vérifier que les messages sont récupérés rapidement en utilisant Redis et que MySQL est consulté uniquement lorsque Redis est vide.

----
## Resolution  

### Architecture du Projet

```
twitter-clone/
│
├── backend/
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── utils/
│   ├── app.js
│   ├── server.js
│   └── config.js
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── App.js
│   │   ├── index.js
│   │   └── styles.css
│   └── package.json
│
├── tests/
│   ├── unit/
│   └── integration/
│
├── package.json
├── README.md
└── .gitignore
```

### Fichier des Dépendances (`package.json`)

Voici le fichier `package.json` initial qui contient les dépendances nécessaires pour le projet :

```json
{
  "name": "twitter-clone",
  "version": "1.0.0",
  "description": "A simple Twitter clone using Node.js, React, Redis, and MySQL",
  "main": "server.js",
  "scripts": {
    "start": "node backend/server.js",
    "dev": "nodemon backend/server.js",
    "test": "jest",
    "client": "cd frontend && npm start",
    "server": "nodemon backend/server.js",
    "build": "cd frontend && npm run build"
  },
  "dependencies": {
    "express": "^4.17.1",
    "mysql2": "^2.3.3",
    "redis": "^4.0.1",
    "cors": "^2.8.5",
    "body-parser": "^1.19.0",
    "jest": "^27.0.6",
    "supertest": "^6.1.3",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-scripts": "^4.0.3",
    "axios": "^0.21.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.7"
  }
}
```

### Étape 1 : Configuration du Backend

#### 1.1 Configuration de Base (`config.js`)

```javascript
// backend/config.js
module.exports = {
  redis: {
    host: '127.0.0.1',
    port: 6379
  },
  mysql: {
    host: 'localhost',
    user: 'root',
    password: 'password',
    database: 'twitter_clone'
  },
  server: {
    port: 5000
  }
};
```

#### 1.2 Création du Serveur (`server.js`)

```javascript
// backend/server.js
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const config = require('./config');
const app = express();

app.use(cors());
app.use(bodyParser.json());

app.listen(config.server.port, () => {
  console.log(`Server running on port ${config.server.port}`);
});
```

#### 1.3 Connexion à Redis et MySQL (`utils/db.js`)

```javascript
// backend/utils/db.js
const redis = require('redis');
const mysql = require('mysql2/promise');
const config = require('../config');

const redisClient = redis.createClient(config.redis);

redisClient.on('error', (err) => {
  console.error('Redis error:', err);
});

const mysqlPool = mysql.createPool(config.mysql);

module.exports = { redisClient, mysqlPool };
```

### Étape 2 : Création des Modèles et Services

#### 2.1 Modèle de Message (`models/Message.js`)

```javascript
// backend/models/Message.js
const { mysqlPool } = require('../utils/db');

class Message {
  static async createMessage(username, content) {
    const [result] = await mysqlPool.execute(
      'INSERT INTO messages (username, content) VALUES (?, ?)',
      [username, content]
    );
    return result.insertId;
  }

  static async getMessages() {
    const [rows] = await mysqlPool.query('SELECT * FROM messages ORDER BY created_at DESC');
    return rows;
  }
}

module.exports = Message;
```

#### 2.2 Service de Cache (`services/cacheService.js`)

```javascript
// backend/services/cacheService.js
const { redisClient } = require('../utils/db');

const cacheService = {
  getMessages: () => {
    return new Promise((resolve, reject) => {
      redisClient.get('messages', (err, data) => {
        if (err) return reject(err);
        resolve(data ? JSON.parse(data) : null);
      });
    });
  },

  setMessages: (messages) => {
    redisClient.set('messages', JSON.stringify(messages));
  }
};

module.exports = cacheService;
```

### Étape 3 : Création des Contrôleurs et Routes

#### 3.1 Contrôleur de Message (`controllers/messageController.js`)

```javascript
// backend/controllers/messageController.js
const Message = require('../models/Message');
const cacheService = require('../services/cacheService');

const messageController = {
  postMessage: async (req, res) => {
    const { username, content } = req.body;
    try {
      const messageId = await Message.createMessage(username, content);
      cacheService.setMessages(await Message.getMessages());
      res.status(201).json({ id: messageId, username, content });
    } catch (err) {
      res.status(500).json({ error: err.message });
    }
  },

  getMessages: async (req, res) => {
    try {
      let messages = await cacheService.getMessages();
      if (!messages) {
        messages = await Message.getMessages();
        cacheService.setMessages(messages);
      }
      res.status(200).json(messages);
    } catch (err) {
      res.status(500).json({ error: err.message });
    }
  }
};

module.exports = messageController;
```

#### 3.2 Routes (`routes/messageRoutes.js`)

```javascript
// backend/routes/messageRoutes.js
const express = require('express');
const messageController = require('../controllers/messageController');

const router = express.Router();

router.post('/messages', messageController.postMessage);
router.get('/messages', messageController.getMessages);

module.exports = router;
```

#### 3.3 Intégration des Routes dans `app.js`

```javascript
// backend/app.js
const express = require('express');
const messageRoutes = require('./routes/messageRoutes');
const app = express();

app.use(express.json());
app.use('/api', messageRoutes);

module.exports = app;
```

### Étape 4 : Configuration du Frontend

#### 4.1 Structure de Base (`frontend/src/App.js`)

```javascript
// frontend/src/App.js
import React, { useState, useEffect } from 'react';
import axios from 'axios';
import './styles.css';

const App = () => {
  const [messages, setMessages] = useState([]);
  const [username, setUsername] = useState('');
  const [content, setContent] = useState('');

  useEffect(() => {
    fetchMessages();
  }, []);

  const fetchMessages = async () => {
    const response = await axios.get('/api/messages');
    setMessages(response.data);
  };

  const postMessage = async () => {
    await axios.post('/api/messages', { username, content });
    setContent('');
    fetchMessages();
  };

  return (
    <div className="app">
      <h1>Twitter Clone</h1>
      <div className="message-form">
        <input
          type="text"
          placeholder="Enter your username"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
        />
        <textarea
          placeholder="Write your message..."
          value={content}
          onChange={(e) => setContent(e.target.value)}
        />
        <button onClick={postMessage}>Post</button>
      </div>
      <div className="messages">
        {messages.map((msg) => (
          <div key={msg.id} className="message">
            <strong>{msg.username}</strong>: {msg.content}
          </div>
        ))}
      </div>
    </div>
  );
};

export default App;
```

#### 4.2 Styles de Base (`frontend/src/styles.css`)

```css
/* frontend/src/styles.css */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f5f8fa;
}

.app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.message-form {
  margin-bottom: 20px;
}

.message-form input,
.message-form textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.message-form button {
  padding: 10px 20px;
  background-color: #1da1f2;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.message-form button:hover {
  background-color: #1991db;
}

.messages {
  background-color: white;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.message {
  margin-bottom: 10px;
  padding: 10px;
  border-bottom: 1px solid #eee;
}

.message:last-child {
  border-bottom: none;
}
```

### Étape 5 : Configuration des Tests avec Jest

#### 5.1 Test de Publication (`tests/unit/messageController.test.js`)

```javascript
// tests/unit/messageController.test.js
const request = require('supertest');
const app = require('../../backend/app');
const { mysqlPool } = require('../../backend/utils/db');

describe('Message Controller', () => {
  beforeAll(async () => {
    await mysqlPool.execute('DELETE FROM messages');
  });

  it('should post a message', async () => {
    const response = await request(app)
      .post('/api/messages')
      .send({ username: 'testuser', content: 'Hello, world!' });
    expect(response.statusCode).toBe(201);
    expect(response.body).toHaveProperty('id');
  });

  it('should get messages', async () => {
    const response = await request(app).get('/api/messages');
    expect(response.statusCode).toBe(200);
    expect(response.body.length).toBeGreaterThan(0);
  });
});
```

#### 5.2 Exécution des Tests

Pour exécuter les tests, utilisez la commande suivante :

```bash
npm test
```

### Étape 6 : Déploiement et Exécution

Pour exécuter l'application en mode développement, utilisez les commandes suivantes :

```bash
npm run server  # Pour démarrer le serveur backend
npm run client  # Pour démarrer le frontend React
```

Pour construire et déployer l'application en production :

```bash
npm run build  # Pour construire le frontend
npm start      # Pour démarrer le serveur en production
```





----
