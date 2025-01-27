# Chapitre 04 : Tests automatisés et déploiement de l'application

Dans ce chapitre, nous allons aborder deux aspects importants du développement d'applications Node.js : les tests automatisés et le déploiement. Nous utiliserons **Jest** et **Supertest** pour les tests, puis nous déploierons notre application sur un service cloud.

---
## Dependances 

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
