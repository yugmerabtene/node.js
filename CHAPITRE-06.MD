### Chapitre 06 : Sécurité des applications Node.js

Dans ce chapitre, nous aborderons les bonnes pratiques et les outils nécessaires pour assurer la sécurité de vos applications Node.js. Les problèmes de sécurité sont fréquents, mais avec les bonnes mesures, vous pouvez éviter les attaques courantes.

#### 1. **Protéger contre les attaques par injection (SQL, NoSQL, etc.)**
Les attaques par injection sont l'un des types d'attaques les plus courants. Il est important de valider et d'assainir toutes les entrées utilisateur. Utilisez des ORM (comme Sequelize pour SQL ou Mongoose pour MongoDB) qui préviennent ces injections en utilisant des requêtes paramétrées.

**Exemple :**
```javascript
const mongoose = require('mongoose');
const User = mongoose.model('User', { username: String, password: String });

User.find({ username: req.body.username }, (err, user) => {
  if (err) throw err;
  res.send(user);
});
```
Cela prévient les injections en utilisant des filtres et des requêtes sécurisées.

#### 2. **Utiliser HTTPS**
Toujours utiliser HTTPS pour sécuriser les communications entre le client et le serveur. Le protocole HTTP est vulnérable aux attaques de type **man-in-the-middle**.

**Exemple de configuration HTTPS avec Node.js :**
```javascript
const https = require('https');
const fs = require('fs');
const app = require('express')();

const options = {
  key: fs.readFileSync('private-key.pem'),
  cert: fs.readFileSync('certificate.pem')
};

https.createServer(options, app).listen(3000, () => {
  console.log('Serveur HTTPS en marche sur le port 3000');
});
```

#### 3. **Gérer les sessions de manière sécurisée**
Utilisez des bibliothèques sécurisées pour gérer les sessions, telles que `express-session`. Assurez-vous que vos cookies sont configurés de manière sécurisée.

**Exemple avec express-session :**
```javascript
const session = require('express-session');
const app = require('express')();

app.use(session({
  secret: 'votre-secret',
  resave: false,
  saveUninitialized: true,
  cookie: { secure: true }  // Assurez-vous que le cookie soit sécurisé
}));

app.listen(3000);
```

#### 4. **Sécuriser les entrées utilisateur**
Toujours valider et assainir toutes les données entrantes. Utilisez des bibliothèques comme **Joi** ou **express-validator** pour valider les données utilisateur.

**Exemple avec express-validator :**
```javascript
const { body, validationResult } = require('express-validator');

app.post('/signup', [
  body('email').isEmail(),
  body('password').isLength({ min: 6 })
], (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  res.send('Inscription réussie');
});
```

#### 5. **Limiter les attaques par force brute**
Utilisez des mécanismes comme le **rate-limiting** pour empêcher les attaques par force brute sur vos API. Vous pouvez utiliser des bibliothèques comme **express-rate-limit** pour limiter le nombre de requêtes par utilisateur.

**Exemple avec express-rate-limit :**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limite chaque IP à 100 requêtes par fenêtre
});

app.use(limiter);
```

#### 6. **Utiliser les en-têtes HTTP sécurisés**
Les en-têtes HTTP comme `Content-Security-Policy`, `Strict-Transport-Security`, et `X-Content-Type-Options` peuvent protéger contre de nombreuses attaques, notamment les attaques de type XSS.

**Exemple avec Helmet :**
```javascript
const helmet = require('helmet');
const app = require('express')();

app.use(helmet()); // Active plusieurs en-têtes de sécurité par défaut
app.listen(3000);
```

#### TP : Sécurisation de votre application

1. **Protéger contre les injections** :
   - Implémentez un ORM et utilisez des requêtes paramétrées.
2. **Forcer l'utilisation de HTTPS** :
   - Mettez en place HTTPS pour sécuriser les communications.
3. **Configurer les sessions de manière sécurisée** :
   - Utilisez `express-session` et configurez correctement les cookies.
4. **Valider et assainir les données utilisateur** :
   - Utilisez **express-validator** ou **Joi** pour valider les entrées utilisateur.
5. **Limiter les requêtes pour éviter les attaques par force brute** :
   - Utilisez **express-rate-limit** pour restreindre le nombre de requêtes par utilisateur.
6. **Appliquer des en-têtes de sécurité HTTP** :
   - Intégrez **Helmet** pour activer les en-têtes sécurisés dans votre application.
