# Chapitre 04 : Tests automatisés et déploiement de l'application

Dans ce chapitre, nous allons aborder deux aspects importants du développement d'applications Node.js : les tests automatisés et le déploiement. Nous utiliserons **Jest** et **Supertest** pour les tests, puis nous déploierons notre application sur un service cloud.

---
## Dependances 
Voici un exemple de fichier `package.json` pour ce projet. Il inclut les dépendances nécessaires pour développer, tester, et déployer l'application Node.js. 

### `package.json`
```json
{
  "name": "nodejs-crud-app",
  "version": "1.0.0",
  "description": "Application Node.js CRUD avec tests automatisés et déploiement.",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "test": "jest"
  },
  "keywords": ["nodejs", "crud", "jest", "supertest", "heroku", "mongodb"],
  "author": "VotreNom",
  "license": "MIT",
  "dependencies": {
    "dotenv": "^16.0.3",
    "express": "^4.18.2",
    "mongoose": "^7.0.0"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "supertest": "^6.3.0",
    "nodemon": "^2.0.22"
  }
}
```

### Explications des sections importantes :
1. **Dépendances principales :**
   - `express` : Framework pour créer des routes et gérer les requêtes.
   - `mongoose` : ORM pour interagir avec MongoDB.
   - `dotenv` : Charger les variables d'environnement.

2. **Dépendances de développement :**
   - `jest` : Framework pour les tests.
   - `supertest` : Librairie pour tester les requêtes HTTP.
   - `nodemon` : Rechargement automatique du serveur en développement.

3. **Scripts :**
   - `start` : Lancer l'application en production.
   - `dev` : Lancer l'application en mode développement (avec `nodemon`).
   - `test` : Exécuter les tests avec Jest.

---
## Code de l'application : 

Voici le code complet pour une application CRUD avec Express et MongoDB, prêt pour intégrer les tests et le déploiement.

---

### 1. Fichier `app.js` (point d'entrée principal)

```javascript
require('dotenv').config();
const express = require('express');
const mongoose = require('mongoose');
const taskRoutes = require('./routes/tasks');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json());

// Connexion à la base de données
const DB_URI = process.env.DB_URI || 'mongodb://localhost:27017/crud_db';
mongoose
  .connect(DB_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connecté à MongoDB'))
  .catch((err) => console.error('Erreur de connexion à MongoDB:', err));

// Routes
app.use('/tasks', taskRoutes);

// Démarrer le serveur
app.listen(PORT, () => {
  console.log(`Serveur démarré sur http://localhost:${PORT}`);
});

module.exports = app;
```

---

### 2. Fichier `models/Task.js` (modèle Mongoose)

```javascript
const mongoose = require('mongoose');

const taskSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    description: { type: String, required: false },
    completed: { type: Boolean, default: false },
  },
  { timestamps: true }
);

module.exports = mongoose.model('Task', taskSchema);
```

---

### 3. Fichier `routes/tasks.js` (routes CRUD)

```javascript
const express = require('express');
const Task = require('../models/Task');
const router = express.Router();

// Créer une tâche
router.post('/', async (req, res) => {
  try {
    const task = new Task(req.body);
    const savedTask = await task.save();
    res.status(201).json(savedTask);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// Récupérer toutes les tâches
router.get('/', async (req, res) => {
  try {
    const tasks = await Task.find();
    res.status(200).json(tasks);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Récupérer une tâche par ID
router.get('/:id', async (req, res) => {
  try {
    const task = await Task.findById(req.params.id);
    if (!task) {
      return res.status(404).json({ message: 'Tâche non trouvée' });
    }
    res.status(200).json(task);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Mettre à jour une tâche
router.put('/:id', async (req, res) => {
  try {
    const updatedTask = await Task.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
      runValidators: true,
    });
    if (!updatedTask) {
      return res.status(404).json({ message: 'Tâche non trouvée' });
    }
    res.status(200).json(updatedTask);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// Supprimer une tâche
router.delete('/:id', async (req, res) => {
  try {
    const deletedTask = await Task.findByIdAndDelete(req.params.id);
    if (!deletedTask) {
      return res.status(404).json({ message: 'Tâche non trouvée' });
    }
    res.status(200).json({ message: 'Tâche supprimée avec succès' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
```

---

### 4. Fichier `.env` (variables d'environnement)

```plaintext
PORT=3000
DB_URI=mongodb://localhost:27017/crud_db
```

---

### 5. Structure du projet

```
/nodejs-crud-app
├── app.js
├── models
│   └── Task.js
├── routes
│   └── tasks.js
├── tests
│   └── (À compléter pour les tests automatisés)
├── .env
├── package.json
```

---

### Instructions pour exécuter le projet
1. **Installer les dépendances** :
   ```bash
   npm install
   ```

2. **Démarrer l'application en mode développement** :
   ```bash
   npm run dev
   ```

3. **Tester les routes avec un outil comme Postman** :
   - `POST /tasks` : Ajouter une tâche.
   - `GET /tasks` : Obtenir toutes les tâches.
   - `GET /tasks/:id` : Obtenir une tâche par ID.
   - `PUT /tasks/:id` : Mettre à jour une tâche.
   - `DELETE /tasks/:id` : Supprimer une tâche.

---

Fichiers de tests automatisés avec **Jest** et **Supertest**. Ces tests couvrent les opérations CRUD de l'application.

---

### 1. Créez le fichier `tests/tasks.test.js`

```javascript
const request = require('supertest');
const mongoose = require('mongoose');
const app = require('../app');
const Task = require('../models/Task');

const TEST_DB_URI = 'mongodb://localhost:27017/test_crud';

// Connexion à une base de données de test avant les tests
beforeAll(async () => {
  await mongoose.connect(TEST_DB_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  });
});

// Nettoyez la base de données après chaque test
afterEach(async () => {
  await Task.deleteMany({});
});

// Déconnectez-vous de la base de données après tous les tests
afterAll(async () => {
  await mongoose.connection.close();
});

// Tests CRUD
describe('Tests CRUD pour les tâches', () => {
  test('Créer une nouvelle tâche', async () => {
    const res = await request(app)
      .post('/tasks')
      .send({
        title: 'Test Task',
        description: 'This is a test task',
      });

    expect(res.statusCode).toBe(201);
    expect(res.body.title).toBe('Test Task');
    expect(res.body.completed).toBe(false);
  });

  test('Récupérer toutes les tâches', async () => {
    await Task.create({ title: 'Test Task', description: 'Test description' });

    const res = await request(app).get('/tasks');
    expect(res.statusCode).toBe(200);
    expect(res.body.length).toBe(1);
    expect(res.body[0].title).toBe('Test Task');
  });

  test('Récupérer une tâche par ID', async () => {
    const task = await Task.create({ title: 'Task by ID', description: 'Find me' });

    const res = await request(app).get(`/tasks/${task._id}`);
    expect(res.statusCode).toBe(200);
    expect(res.body.title).toBe('Task by ID');
  });

  test('Mettre à jour une tâche', async () => {
    const task = await Task.create({ title: 'Old Task', description: 'Old description' });

    const res = await request(app)
      .put(`/tasks/${task._id}`)
      .send({ title: 'Updated Task', description: 'Updated description' });

    expect(res.statusCode).toBe(200);
    expect(res.body.title).toBe('Updated Task');
    expect(res.body.description).toBe('Updated description');
  });

  test('Supprimer une tâche', async () => {
    const task = await Task.create({ title: 'Task to delete', description: 'Delete me' });

    const res = await request(app).delete(`/tasks/${task._id}`);
    expect(res.statusCode).toBe(200);
    expect(res.body.message).toBe('Tâche supprimée avec succès');

    const deletedTask = await Task.findById(task._id);
    expect(deletedTask).toBeNull();
  });

  test('Erreur 404 pour une tâche inexistante', async () => {
    const fakeId = new mongoose.Types.ObjectId();

    const res = await request(app).get(`/tasks/${fakeId}`);
    expect(res.statusCode).toBe(404);
    expect(res.body.message).toBe('Tâche non trouvée');
  });
});
```

---

### 2. Instructions pour exécuter les tests

1. **Configurer la base de données de test** :
   Assurez-vous que vous avez une base MongoDB nommée `test_crud` qui tourne sur votre machine locale ou dans le cloud.

2. **Lancer les tests avec Jest** :
   ```bash
   npm test
   ```

3. **Sortie attendue** :  
   Si tout fonctionne, vous verrez une sortie indiquant que tous les tests ont passé avec succès.

---

### Structure complète après ajout des tests

```
/nodejs-crud-app
├── app.js
├── models
│   └── Task.js
├── routes
│   └── tasks.js
├── tests
│   └── tasks.test.js
├── .env
├── package.json
```

---

---

## 1. Tester une application Node.js

Les tests permettent de garantir la qualité et la stabilité de votre application. Nous allons configurer Jest et Supertest pour tester les routes de notre application CRUD.

### Étape 1 : Installer les dépendances
Ajoutez Jest et Supertest comme dépendances de développement :
```bash
npm install --save-dev jest supertest
```

Dans `package.json`, ajoutez un script pour exécuter les tests :
```json
"scripts": {
  "test": "jest"
}
```

### Étape 2 : Configurer Jest
Créez un fichier `jest.config.js` :
```javascript
module.exports = {
    testEnvironment: 'node',
};
```

### Étape 3 : Écrire des tests
Créez un fichier de test dans le dossier `tests`, par exemple `tests/tasks.test.js` :
```javascript
const request = require('supertest');
const app = require('../app');
const mongoose = require('mongoose');
const Task = require('../models/Task');

// Connectez-vous à une base de données de test avant les tests
beforeAll(async () => {
    await mongoose.connect('mongodb://localhost:27017/test_crud', {
        useNewUrlParser: true,
        useUnifiedTopology: true,
    });
});

// Nettoyez la base de données après chaque test
afterEach(async () => {
    await Task.deleteMany({});
});

// Déconnectez-vous de la base de données après tous les tests
afterAll(async () => {
    await mongoose.connection.close();
});

// Tests
describe('Tests CRUD pour les tâches', () => {
    test('Créer une nouvelle tâche', async () => {
        const res = await request(app)
            .post('/tasks')
            .send({
                title: 'Test Task',
                description: 'This is a test task',
            });

        expect(res.statusCode).toBe(201);
        expect(res.body.title).toBe('Test Task');
    });

    test('Récupérer toutes les tâches', async () => {
        await Task.create({ title: 'Test Task', description: 'Test description' });

        const res = await request(app).get('/tasks');
        expect(res.statusCode).toBe(200);
        expect(res.body.length).toBe(1);
    });

    test('Mettre à jour une tâche', async () => {
        const task = await Task.create({ title: 'Task 1', description: 'Description 1' });

        const res = await request(app)
            .put(`/tasks/${task._id}`)
            .send({ title: 'Updated Task' });

        expect(res.statusCode).toBe(200);
        expect(res.body.title).toBe('Updated Task');
    });

    test('Supprimer une tâche', async () => {
        const task = await Task.create({ title: 'Task to delete', description: 'Delete me' });

        const res = await request(app).delete(`/tasks/${task._id}`);
        expect(res.statusCode).toBe(200);

        const deletedTask = await Task.findById(task._id);
        expect(deletedTask).toBeNull();
    });
});
```

### Étape 4 : Exécuter les tests
Lancez vos tests avec la commande :
```bash
npm test
```

---

## 2. Déploiement de l'application

Une fois que vos tests passent avec succès, vous pouvez déployer l'application.

### Étape 1 : Préparer l'application pour le déploiement
1. Modifiez votre `app.js` pour utiliser une variable d'environnement pour le port et la base de données :
```javascript
const PORT = process.env.PORT || 3000;
const DB_URI = process.env.DB_URI || 'mongodb://localhost:27017/crud_db';

mongoose.connect(DB_URI, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('Connecté à MongoDB'))
    .catch(err => console.error('Erreur de connexion à MongoDB:', err));

app.listen(PORT, () => console.log(`Serveur démarré sur http://localhost:${PORT}`));
```

2. Créez un fichier `.env` pour stocker les variables d'environnement :
```
PORT=3000
DB_URI=mongodb://localhost:27017/crud_db
```

3. Installez le package `dotenv` pour charger ces variables :
```bash
npm install dotenv
```

Ajoutez cette ligne en haut de `app.js` :
```javascript
require('dotenv').config();
```

### Étape 2 : Déployer sur Heroku
1. **Installer l'outil Heroku CLI** : [Téléchargez et installez Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).
2. **Se connecter à Heroku** :
   ```bash
   heroku login
   ```
3. **Créer une application Heroku** :
   ```bash
   heroku create app-crud
   ```
4. **Pousser le code sur Heroku** :
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   heroku git:remote -a app-crud
   git push heroku main
   ```

5. **Configurer les variables d'environnement** :
   ```bash
   heroku config:set DB_URI=mongodb+srv://<votre-uri-mongodb>
   ```

6. **Ouvrir l'application** :
   ```bash
   heroku open
   ```

---

## 3. Prochain chapitre

Dans le prochain chapitre, nous aborderons les optimisations des performances pour les applications Node.js, comme l'utilisation de clusters, la mise en cache avec Redis, et des techniques avancées pour améliorer la scalabilité.
