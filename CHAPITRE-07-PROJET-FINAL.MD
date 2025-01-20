### Projet Final : Clone de Twitter (X) - Application Web

#### Cahier des Charges

**Objectif du projet :**
Créer une application web qui simule les fonctionnalités principales de la plateforme Twitter (renommée ici X), permettant aux utilisateurs de publier des tweets, suivre d'autres utilisateurs, et interagir avec des publications via des "likes", "replies" et "retweets".

#### Fonctionnalités principales :

1. **Page d'inscription / Connexion**
   - Permet aux utilisateurs de créer un compte ou de se connecter.
   - Doit inclure la validation d'email, la gestion des mots de passe sécurisés et l'authentification via JWT.

2. **Dashboard (Fil d'actualités)**
   - L'utilisateur voit les tweets de ceux qu'il suit, triés par ordre chronologique ou par pertinence.
   - Affichage de tweets avec des informations sur l'utilisateur, le nombre de likes, de retweets et de réponses.

3. **Publication de tweets**
   - Les utilisateurs peuvent publier des tweets jusqu'à 280 caractères.
   - Ajout d'images et de liens dans les tweets.

4. **Interactions avec les tweets**
   - Les utilisateurs peuvent "liker", "retweeter" et répondre à un tweet.
   - Affichage en temps réel des interactions sur un tweet (nombre de likes, retweets, réponses).

5. **Suivi et gestion des utilisateurs**
   - Les utilisateurs peuvent suivre et se désabonner d'autres utilisateurs.
   - Affichage des profils avec des informations publiques (bio, photo de profil).

6. **Notifications**
   - Notifications en temps réel lorsqu'un utilisateur est mentionné, qu'un tweet est aimé ou retweeté.

7. **Recherche de tweets**
   - Permet de rechercher des tweets par mot-clé, hashtag ou utilisateur.

8. **Paramètres de l'utilisateur**
   - L'utilisateur peut modifier son profil (photo, bio, etc.).
   - Gestion des paramètres de sécurité et de confidentialité.

9. **Déconnexion**
   - Permet à l'utilisateur de se déconnecter de la plateforme.

---

### User Stories Détailées

1. **En tant qu'utilisateur**, je veux pouvoir créer un compte afin de rejoindre la plateforme.
   - **Critères d'acceptation** :
     - L'utilisateur peut s'inscrire en fournissant une adresse email, un nom d'utilisateur, et un mot de passe sécurisé.
     - L'utilisateur reçoit un email de confirmation pour activer son compte.
   
2. **En tant qu'utilisateur**, je veux pouvoir me connecter à la plateforme avec mon nom d'utilisateur et mon mot de passe afin d'accéder à mon compte.
   - **Critères d'acceptation** :
     - L'utilisateur peut se connecter avec un formulaire simple.
     - Si les informations sont incorrectes, un message d'erreur s'affiche.

3. **En tant qu'utilisateur**, je veux pouvoir publier des tweets afin de partager des pensées avec mes abonnés.
   - **Critères d'acceptation** :
     - L'utilisateur peut entrer jusqu'à 280 caractères dans le champ de texte.
     - Un tweet peut inclure un lien ou une image.
     - Une fois le tweet posté, il apparaît immédiatement dans le fil d'actualités.

4. **En tant qu'utilisateur**, je veux pouvoir aimer et retweeter des tweets afin de participer à la conversation.
   - **Critères d'acceptation** :
     - L'utilisateur peut "liker" un tweet en cliquant sur un bouton de cœur.
     - L'utilisateur peut "retweeter" un tweet avec ou sans commentaire.
     - Le nombre de likes et retweets est mis à jour en temps réel.

5. **En tant qu'utilisateur**, je veux pouvoir suivre et me désabonner d'autres utilisateurs afin de voir leurs tweets dans mon fil d'actualités.
   - **Critères d'acceptation** :
     - L'utilisateur peut suivre un autre utilisateur en cliquant sur un bouton "Suivre".
     - L'utilisateur peut se désabonner d'un utilisateur en cliquant sur "Se désabonner".
     - Le fil d'actualités de l'utilisateur est mis à jour en fonction des personnes suivies.

6. **En tant qu'utilisateur**, je veux pouvoir recevoir des notifications lorsque quelqu'un aime ou répond à mon tweet.
   - **Critères d'acceptation** :
     - L'utilisateur reçoit une notification en temps réel pour chaque interaction sur ses tweets.

7. **En tant qu'utilisateur**, je veux pouvoir rechercher des tweets par mots-clés ou hashtags pour découvrir de nouvelles conversations.
   - **Critères d'acceptation** :
     - L'utilisateur peut entrer des mots-clés ou des hashtags dans un champ de recherche.
     - Les résultats de la recherche sont affichés avec les tweets correspondants.

8. **En tant qu'utilisateur**, je veux pouvoir consulter et modifier mon profil afin d'ajouter des informations personnelles.
   - **Critères d'acceptation** :
     - L'utilisateur peut mettre à jour sa photo de profil, son nom, sa bio et son adresse email.

---

### Critères de Validation

1. **Fonctionnalité d'inscription** :
   - Vérifier qu'un email de confirmation est envoyé après l'inscription.
   - Vérifier que l'utilisateur est redirigé vers la page de connexion après une inscription réussie.

2. **Connexion sécurisée** :
   - Tester la connexion avec des identifiants valides et invalides.
   - Vérifier que les sessions sont correctement gérées avec des tokens JWT.

3. **Publication de tweets** :
   - Vérifier que le tweet est posté correctement, même avec des images ou des liens.
   - Tester l'affichage du tweet dans le fil d'actualités.

4. **Interactions avec les tweets** :
   - Tester les actions de like et retweet.
   - Vérifier que le nombre de likes et retweets est mis à jour en temps réel.

5. **Suivi d'utilisateurs** :
   - Tester l'action de suivi et de désabonnement d'un utilisateur.
   - Vérifier que le profil de l'utilisateur et son fil d'actualités sont mis à jour en conséquence.

6. **Notifications en temps réel** :
   - Vérifier la réception des notifications lorsque quelqu'un interagit avec un tweet.

7. **Recherche de tweets** :
   - Tester la recherche par mots-clés et hashtags.
   - Vérifier que les résultats sont correctement filtrés et affichés.

8. **Sécurité des sessions et mots de passe** :
   - Tester la gestion des sessions avec JWT.
   - Vérifier que les mots de passe sont stockés de manière sécurisée avec un algorithme de hachage.

---

### Technologies suggérées pour le projet :

- **Frontend** : React.js, HTML, CSS
- **Backend** : Node.js, Express
- **Base de données** : MongoDB ou PostgreSQL
- **Authentification** : JWT (JSON Web Token)
- **Notifications en temps réel** : Socket.io
- **Gestion des sessions** : Express-session
- **Sécurisation** : Helmet.js, bcryptjs pour le hachage des mots de passe

---
