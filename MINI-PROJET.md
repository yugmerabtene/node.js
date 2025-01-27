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
