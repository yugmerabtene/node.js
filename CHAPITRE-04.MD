# Développement de l'application Node.js CRUD avec MongoDB

Ce guide couvre :
1. **Installation et configuration de MongoDB.**
2. **Développement complet de l'application (modèle, routes, serveur).**
3. **Ajout de tests automatisés avec Jest et Supertest.**
4. **Déploiement sur Heroku.**

---

## 1. Installation et configuration de MongoDB

### Étape 1 : Installer MongoDB
- Téléchargez MongoDB depuis le site officiel : [MongoDB Community Server](https://www.mongodb.com/try/download/community).
- Installez MongoDB en choisissant les paramètres par défaut.

### Étape 2 : Lancer le serveur MongoDB
1. Créez un dossier pour stocker les données (ex. `C:\data\db`).
2. Lancez MongoDB depuis le terminal :
   ```bash
   mongod --dbpath=C:\data\db
   ```
3. Vérifiez que MongoDB fonctionne en exécutant `mongo` dans un autre terminal.

---

## 2. Développement de l'application CRUD

### Structure du projet
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

### Étape 1 : Initialiser le projet
1. Créez un nouveau projet Node.js :
   ```bash
   mkdir nodejs-crud-app
   cd nodejs-crud-app
   npm init -y
   ```

2. Installez les dépendances :
   ```bash
   npm install express mongoose dotenv
   npm install --save-dev jest supertest nodemon
   ```

3. Créez le fichier `package.json` (ou modifiez-le) :
   ```json
   {
     "name": "nodejs-crud-app",
     "version": "1.0.0",
     "main": "app.js",
     "scripts": {
       "start": "node app.js",
       "dev": "nodemon app.js",
       "test": "jest"
     },
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

---

### Étape 2 : Configurer l'application

#### Fichier `.env` (variables d'environnement)
```plaintext
PORT=3000
DB_URI=mongodb://localhost:27017/crud_db
```

#### Fichier `app.js`
```javascript
require('dotenv').config();
const express = require('express');
const mongoose = require('mongoose');
const taskRoutes = require('./routes/tasks');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json());

// Connexion à MongoDB
const DB_URI = process.env.DB_URI || 'mongodb://localhost:27017/crud_db';
mongoose
  .connect(DB_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connecté à MongoDB'))
  .catch((err) => console.error('Erreur de connexion à MongoDB:', err));

// Routes
app.use('/tasks', taskRoutes);

// Lancer le serveur
app.listen(PORT, () => {
  console.log(`Serveur démarré sur http://localhost:${PORT}`);
});

module.exports = app;
```

---

#### Fichier `models/Task.js` (modèle Mongoose)
```javascript
const mongoose = require('mongoose');

const taskSchema = new mongoose.Schema(
  {
    title: { type: String, required: true },
    description: { type: String },
    completed: { type: Boolean, default: false },
  },
  { timestamps: true }
);

module.exports = mongoose.model('Task', taskSchema);
```

---

#### Fichier `routes/tasks.js` (routes CRUD)
```javascript
const express = require('express');
const Task = require('../models/Task');
const router = express.Router();

// Ajouter une tâche
router.post('/', async (req, res) => {
  try {
    const task = new Task(req.body);
    const savedTask = await task.save();
    res.status(201).json(savedTask);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Récupérer toutes les tâches
router.get('/', async (req, res) => {
  try {
    const tasks = await Task.find();
    res.status(200).json(tasks);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Mettre à jour une tâche
router.put('/:id', async (req, res) => {
  try {
    const updatedTask = await Task.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
      runValidators: true,
    });
    res.status(200).json(updatedTask);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Supprimer une tâche
router.delete('/:id', async (req, res) => {
  try {
    await Task.findByIdAndDelete(req.params.id);
    res.status(200).json({ message: 'Tâche supprimée avec succès' });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

module.exports = router;
```

---

## 3. Tests avec Jest et Supertest

#### Fichier `tests/tasks.test.js`
```javascript
const request = require('supertest');
const app = require('../app');
const mongoose = require('mongoose');
const Task = require('../models/Task');

beforeAll(async () => {
  await mongoose.connect('mongodb://localhost:27017/test_crud', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  });
});

afterEach(async () => {
  await Task.deleteMany({});
});

afterAll(async () => {
  await mongoose.connection.close();
});

describe('CRUD Tests', () => {
  test('Créer une tâche', async () => {
    const res = await request(app).post('/tasks').send({ title: 'Test Task' });
    expect(res.statusCode).toBe(201);
    expect(res.body.title).toBe('Test Task');
  });

  test('Récupérer toutes les tâches', async () => {
    await Task.create({ title: 'Test Task' });
    const res = await request(app).get('/tasks');
    expect(res.body.length).toBe(1);
  });
});
```

Lancez les tests :
```bash
npm test
```

---
