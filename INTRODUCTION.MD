# Introduction à Node.js

Node.js est un environnement d'exécution JavaScript côté serveur, basé sur le moteur V8 de Google Chrome. Il permet d'exécuter du JavaScript en dehors du navigateur, offrant ainsi la possibilité de créer des applications côté serveur rapides et scalables.

## Caractéristiques principales de Node.js

- **Non-bloquant et à événements** : Node.js utilise un modèle à événements et non-bloquant pour traiter les opérations d'E/S.
- **Rapide** : Node.js est rapide grâce à son moteur V8 et son architecture.
- **Un seul langage** : Vous pouvez utiliser JavaScript pour le front-end et le back-end.

## Installation de Node.js

1. **Téléchargez Node.js** : Rendez-vous sur le site officiel [https://nodejs.org](https://nodejs.org) et téléchargez la version LTS recommandée.
2. **Installez Node.js** : Suivez les instructions pour votre système d'exploitation.
3. **Vérifiez l'installation** :

```bash
node -v   # Affiche la version de Node.js
npm -v    # Affiche la version de npm (Node Package Manager)
```
# Introduction à Node.js

Node.js est un environnement d'exécution JavaScript côté serveur, basé sur le moteur V8 de Google Chrome. Il permet d'exécuter du JavaScript en dehors du navigateur, offrant ainsi la possibilité de créer des applications côté serveur rapides et scalables.

## Caractéristiques principales de Node.js

- **Non-bloquant et à événements** : Node.js utilise un modèle à événements et non-bloquant pour traiter les opérations d'E/S.
- **Rapide** : Node.js est rapide grâce à son moteur V8 et son architecture.
- **Un seul langage** : Vous pouvez utiliser JavaScript pour le front-end et le back-end.

## Installation de Node.js

1. **Téléchargez Node.js** : Rendez-vous sur le site officiel [https://nodejs.org](https://nodejs.org) et téléchargez la version LTS recommandée.
2. **Installez Node.js** : Suivez les instructions pour votre système d'exploitation.
3. **Vérifiez l'installation** :

```bash
node -v   # Affiche la version de Node.js
npm -v    # Affiche la version de npm (Node Package Manager)
```

---
---

# Premier programme en Node.js

Créons un programme simple qui affiche « Bonjour, Node.js ! ».

1. **Créez un fichier `app.js`** :
   
```javascript
// app.js
console.log('Bonjour, Node.js !');
```

2. **Exécutez le fichier** :

```bash
node app.js
```

Vous devriez voir le message s'afficher dans le terminal.

---

# Modules en Node.js

Node.js utilise des modules pour organiser le code. Un module peut être un fichier ou un ensemble de fonctions.

### Importer des modules internes

Node.js fournit des modules internes, comme `fs` pour le système de fichiers ou `http` pour créer un serveur.

#### Exemple : Lecture d'un fichier

Créez un fichier `data.txt` avec le contenu suivant :

```
Bonjour, Node.js !
```

Code pour lire ce fichier :

```javascript
const fs = require('fs');

fs.readFile('data.txt', 'utf8', (err, data) => {
    if (err) {
        console.error('Erreur lors de la lecture du fichier :', err);
        return;
    }
    console.log('Contenu du fichier :', data);
});
```

### Créer des modules personnalisés

1. **Créez un fichier `math.js`** :

```javascript
// math.js
exports.add = (a, b) => a + b;
exports.subtract = (a, b) => a - b;
```

2. **Utilisez le module** :

```javascript
// app.js
const math = require('./math');

console.log('Addition :', math.add(5, 3));
console.log('Soustraction :', math.subtract(5, 3));
```

---

# Création d'un serveur HTTP basique

Utilisons le module `http` pour créer un serveur.

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Bonjour, Node.js !\n');
});

const PORT = 3000;
server.listen(PORT, () => {
    console.log(`Serveur en cours d'exécution sur http://localhost:${PORT}`);
});
```

Démarrez le serveur avec :

```bash
node app.js
```

Puis accédez à [http://localhost:3000](http://localhost:3000) dans votre navigateur.

---

# TP : Créer une API REST simple

### Objectif

Créer une API REST pour gérer une liste de tâches.

### Étapes

1. **Initialisez un projet Node.js** :

```bash
npm init -y
```

2. **Installez Express** :

```bash
npm install express
```

3. **Créez un fichier `server.js`** :

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

let tasks = [
    { id: 1, title: 'Apprendre Node.js', completed: false },
    { id: 2, title: 'Pratiquer Express', completed: true }
];

// Obtenir toutes les tâches
app.get('/tasks', (req, res) => {
    res.json(tasks);
});

// Ajouter une nouvelle tâche
app.post('/tasks', (req, res) => {
    const newTask = {
        id: tasks.length + 1,
        title: req.body.title,
        completed: false
    };
    tasks.push(newTask);
    res.status(201).json(newTask);
});

// Mettre à jour une tâche
app.put('/tasks/:id', (req, res) => {
    const task = tasks.find(t => t.id === parseInt(req.params.id));
    if (!task) return res.status(404).send('Tâche non trouvée');

    task.title = req.body.title || task.title;
    task.completed = req.body.completed !== undefined ? req.body.completed : task.completed;
    res.json(task);
});

// Supprimer une tâche
app.delete('/tasks/:id', (req, res) => {
    const taskIndex = tasks.findIndex(t => t.id === parseInt(req.params.id));
    if (taskIndex === -1) return res.status(404).send('Tâche non trouvée');

    const deletedTask = tasks.splice(taskIndex, 1);
    res.json(deletedTask);
});

app.listen(PORT, () => {
    console.log(`Serveur API en cours d'exécution sur http://localhost:${PORT}`);
});
```

4. **Testez l'API** :
   - Utilisez un outil comme Postman ou Curl pour tester les routes :
     - `GET /tasks`
     - `POST /tasks`
     - `PUT /tasks/:id`
     - `DELETE /tasks/:id`

---
Tester votre API avec `curl`. Assurez-vous que votre serveur Node.js est en cours d'exécution sur **http://localhost:3000**.

---

### **1. Obtenir toutes les tâches (`GET /tasks`)**
Commande :
```bash
curl -X GET http://localhost:3000/tasks
```

Explication :
- `-X GET` : Indique que c'est une requête HTTP GET.
- Renvoie la liste des tâches au format JSON.

---

### **2. Ajouter une nouvelle tâche (`POST /tasks`)**
Commande :
```bash
curl -X POST http://localhost:3000/tasks -H "Content-Type: application/json" -d '{"title": "Nouvelle tâche"}'
```

Explication :
- `-X POST` : Indique une requête HTTP POST.
- `-H "Content-Type: application/json"` : Ajoute un en-tête HTTP pour spécifier que le corps de la requête est en JSON.
- `-d '{"title": "Nouvelle tâche"}'` : Données envoyées dans le corps de la requête.

---

### **3. Mettre à jour une tâche (`PUT /tasks/:id`)**
Commande :
```bash
curl -X PUT http://localhost:3000/tasks/1 -H "Content-Type: application/json" -d '{"title": "Apprendre Node.js en profondeur", "completed": true}'
```

Explication :
- `http://localhost:3000/tasks/1` : Met à jour la tâche avec `id=1`.
- `-d '{"title": "Apprendre Node.js en profondeur", "completed": true}'` : Envoie les nouvelles valeurs pour `title` et `completed`.

---

### **4. Supprimer une tâche (`DELETE /tasks/:id`)**
Commande :
```bash
curl -X DELETE http://localhost:3000/tasks/1
```

Explication :
- `-X DELETE` : Indique une requête HTTP DELETE.
- `http://localhost:3000/tasks/1` : Supprime la tâche avec `id=1`.

---

### **Exemples combinés**
- Pour vérifier si l'ajout ou la mise à jour a fonctionné, relancez la commande `GET /tasks` pour voir les modifications dans la liste des tâches.

### **Test rapide avec des étapes :**
1. **Lister les tâches** :
   ```bash
   curl -X GET http://localhost:3000/tasks
   ```

2. **Ajouter une tâche** :
   ```bash
   curl -X POST http://localhost:3000/tasks -H "Content-Type: application/json" -d '{"title": "Nouvelle tâche"}'
   ```

3. **Vérifier la nouvelle tâche** :
   ```bash
   curl -X GET http://localhost:3000/tasks
   ```

4. **Mettre à jour une tâche** :
   ```bash
   curl -X PUT http://localhost:3000/tasks/1 -H "Content-Type: application/json" -d '{"completed": true}'
   ```

5. **Supprimer une tâche** :
   ```bash
   curl -X DELETE http://localhost:3000/tasks/1
   ```

Ces commandes vous permettent de tester toutes les fonctionnalités de votre API directement depuis le terminal. 🚀
