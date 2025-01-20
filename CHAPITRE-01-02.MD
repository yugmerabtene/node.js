# Chapitre 01 : Les bases de Node.js

Dans ce chapitre, nous allons explorer les bases de Node.js, y compris la gestion des modules, le système de fichiers et les streams.

## 1. Comprendre le fonctionnement des modules

Node.js utilise des modules pour organiser le code. Ces modules peuvent être internes, externes ou personnalisés.

### 1.1 Modules internes

Node.js fournit des modules internes, comme `fs` pour le système de fichiers ou `path` pour manipuler les chemins de fichiers.

#### Exemple : Utilisation du module `path`

```javascript
const path = require('path');

const cheminAbsolu = path.resolve('data.txt');
console.log('Chemin absolu :', cheminAbsolu);
```

### 1.2 Modules externes

Vous pouvez installer des modules externes via npm. Par exemple, le module `lodash` fournit de nombreuses fonctions utilitaires.

```bash
npm install lodash
```

Code :

```javascript
const _ = require('lodash');

const array = [1, 2, 3, 4];
console.log('Shuffled array :', _.shuffle(array));
```

### 1.3 Créer des modules personnalisés

Créons un module `utils.js` :

```javascript
// utils.js
exports.hello = (name) => `Bonjour, ${name} !`;
```

Utilisation dans un autre fichier :

```javascript
// app.js
const utils = require('./utils');

console.log(utils.hello('Node.js'));
```

## 2. Système de fichiers avec `fs`

Le module `fs` permet de travailler avec le système de fichiers.

### Lecture de fichiers

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

### Écriture dans un fichier

```javascript
fs.writeFile('output.txt', 'Hello Node.js!', (err) => {
    if (err) {
        console.error('Erreur lors de l\'écriture du fichier :', err);
        return;
    }
    console.log('Fichier écrit avec succès !');
});
```

## 3. Streams en Node.js

Les streams permettent de manipuler des données de manière asynchrone et efficace.

### Exemple : Lecture d'un fichier en stream

```javascript
const fs = require('fs');

const readStream = fs.createReadStream('data.txt', 'utf8');

readStream.on('data', (chunk) => {
    console.log('Chunk reçu :', chunk);
});

readStream.on('end', () => {
    console.log('Lecture terminée.');
});

readStream.on('error', (err) => {
    console.error('Erreur :', err);
});
```

---

# Prochain chapitre

Dans le prochain chapitre, nous aborderons des concepts plus avancés comme les middlewares, la gestion des erreurs et l'intégration avec des bases de données. Vous pouvez commencer à explorer les différentes façons d'organiser un projet Node.js pour des applications complexes.

---

# Chapitre 02 : Concepts avancés de Node.js

Dans ce chapitre, nous allons plonger plus profondément dans les fonctionnalités avancées de Node.js, comme la gestion des erreurs, les middlewares, et la gestion des bases de données.

## 1. Les middlewares dans Node.js avec Express

Un middleware est une fonction qui intercepte les requêtes et réponses dans une application Express.

### Exemple de middleware simple

```javascript
const express = require('express');
const app = express();

// Middleware pour journaliser les requêtes
app.use((req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
});

app.get('/', (req, res) => {
    res.send('Bonjour depuis Express avec middleware !');
});

const PORT = 3000;
app.listen(PORT, () => {
    console.log(`Serveur démarré sur http://localhost:${PORT}`);
});
```

## 2. Gestion des erreurs

Node.js permet une gestion fine des erreurs, que ce soit dans les callbacks, avec des `try...catch`, ou via des middlewares d'erreur dans Express.

### Exemple : Middleware de gestion des erreurs

```javascript
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).send('Une erreur est survenue !');
});
```

## 3. Connexion à une base de données

Nous utiliserons MongoDB pour cet exemple. Installez le package mongoose :

```bash
npm install mongoose
```

### Exemple de connexion

```javascript
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/testdb', {
    useNewUrlParser: true,
    useUnifiedTopology: true
}).then(() => {
    console.log('Connecté à MongoDB');
}).catch(err => {
    console.error('Erreur de connexion à MongoDB :', err);
});
```
