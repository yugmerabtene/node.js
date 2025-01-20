# Chapitre 03 : Construire une application CRUD avec Node.js, Express et MongoDB

Dans ce chapitre, nous allons mettre en pratique les concepts appris pour construire une application CRUD (Créer, Lire, Mettre à jour, Supprimer) avec Express et MongoDB.

## 1. Initialisation du projet

### Étape 1 : Créer un projet Node.js
```bash
mkdir app-crud
cd app-crud
npm init -y
```

### Étape 2 : Installer les dépendances nécessaires
```bash
npm install express mongoose body-parser cors
```

### Étape 3 : Structure du projet
Voici une structure suggérée pour organiser le projet :
```
app-crud/
├── models/
│   └── Task.js
├── routes/
│   └── tasks.js
├── app.js
└── package.json
```

---

## 2. Connexion à MongoDB

Dans `app.js` :
```javascript
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');
const cors = require('cors');

const app = express();

// Middleware
app.use(bodyParser.json());
app.use(cors());

// Connexion à MongoDB
mongoose.connect('mongodb://localhost:27017/crud_db', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
})
.then(() => console.log('Connecté à MongoDB'))
.catch(err => console.error('Erreur de connexion à MongoDB:', err));

// Importer les routes
const tasksRoutes = require('./routes/tasks');
app.use('/tasks', tasksRoutes);

// Démarrer le serveur
const PORT = 3000;
app.listen(PORT, () => console.log(`Serveur démarré sur http://localhost:${PORT}`));
```

---

## 3. Créer un modèle Mongoose

Dans `models/Task.js` :
```javascript
const mongoose = require('mongoose');

const TaskSchema = new mongoose.Schema({
    title: { type: String, required: true },
    description: { type: String, required: true },
    completed: { type: Boolean, default: false },
}, { timestamps: true });

module.exports = mongoose.model('Task', TaskSchema);
```

---

## 4. Créer les routes

Dans `routes/tasks.js` :
```javascript
const express = require('express');
const Task = require('../models/Task');

const router = express.Router();

// Route pour créer une tâche
router.post('/', async (req, res) => {
    try {
        const task = new Task(req.body);
        const savedTask = await task.save();
        res.status(201).json(savedTask);
    } catch (err) {
        res.status(400).json({ error: err.message });
    }
});

// Route pour récupérer toutes les tâches
router.get('/', async (req, res) => {
    try {
        const tasks = await Task.find();
        res.json(tasks);
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

// Route pour récupérer une tâche par ID
router.get('/:id', async (req, res) => {
    try {
        const task = await Task.findById(req.params.id);
        if (!task) return res.status(404).json({ error: 'Tâche non trouvée' });
        res.json(task);
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

// Route pour mettre à jour une tâche
router.put('/:id', async (req, res) => {
    try {
        const updatedTask = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
        if (!updatedTask) return res.status(404).json({ error: 'Tâche non trouvée' });
        res.json(updatedTask);
    } catch (err) {
        res.status(400).json({ error: err.message });
    }
});

// Route pour supprimer une tâche
router.delete('/:id', async (req, res) => {
    try {
        const deletedTask = await Task.findByIdAndDelete(req.params.id);
        if (!deletedTask) return res.status(404).json({ error: 'Tâche non trouvée' });
        res.json({ message: 'Tâche supprimée avec succès' });
    } catch (err) {
        res.status(500).json({ error: err.message });
    }
});

module.exports = router;
```

---

## 5. Tester l'application

### Utiliser Postman pour tester les routes
1. **Créer une tâche** : POST `http://localhost:3000/tasks` avec un body JSON :
    ```json
    {
        "title": "Acheter du lait",
        "description": "Aller au supermarché pour acheter du lait"
    }
    ```
2. **Récupérer toutes les tâches** : GET `http://localhost:3000/tasks`
3. **Récupérer une tâche par ID** : GET `http://localhost:3000/tasks/:id`
4. **Mettre à jour une tâche** : PUT `http://localhost:3000/tasks/:id`
5. **Supprimer une tâche** : DELETE `http://localhost:3000/tasks/:id`

---
