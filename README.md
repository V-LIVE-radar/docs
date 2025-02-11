# Documentation de V-LIVE.radar

Bienvenue dans la documentation complète de **NUMMU Bot**, votre outil Discord dédié à la gestion et à la mise en relation dans le secteur immobilier. Ce bot, dont le code source compte plus de 7000 lignes, offre de nombreuses fonctionnalités telles que la recherche géographique, la gestion d’annonces et de favoris, la mise en relation inversée entre acheteurs et prospecteurs, ainsi que des outils de modération et la gestion d’abonnements.

> **Remarque :** Toute cette documentation est désormais centralisée dans ce fichier README.md afin de faciliter la lecture et l’édition. Vous pourrez, ultérieurement, migrer ce contenu dans un générateur de site statique (ex. Jekyll) si vous le souhaitez.

---

## Table des Matières

1. [Premiers Pas](#premiers-pas)
2. [Utiliser Discord](#utiliser-discord)
3. [Commandes et Outils](#commandes-et-outils)
4. [Gestion du Compte](#gestion-du-compte)
5. [Modération et Administration](#modération-et-administration)
6. [Paiement et Abonnements](#paiement-et-abonnements)
7. [Fonctionnalités Avancées](#fonctionnalités-avancées)
8. [Architecture et Développement](#architecture-et-développement)

---

## 1. Premiers Pas

### Démarrage rapide

- **Invitation & Configuration :**  
  Pour commencer, invitez NUMMU Bot sur votre serveur Discord en utilisant l’URL d’invitation fournie dans la documentation interne.  
  Configurez ensuite les paramètres essentiels (ID du serveur, salons dédiés, rôles administratifs, etc.) dans le fichier de configuration `settings.py`.

- **Installation des Dépendances :**  
  Le bot utilise notamment :
  - `discord.py` pour les interactions Discord (commandes classiques et slash),
  - `motor` (AsyncIOMotorClient) pour l’accès à MongoDB,
  - `cachetools` pour le caching avec TTL,
  - `aiohttp`, `asyncio` et d’autres modules standards.
  
  Assurez-vous d’installer les dépendances via pip (par exemple via un fichier `requirements.txt`).

### Démarrage via Discord

- **Synchronisation des Commandes :**  
  Lors du démarrage, le bot synchronise ses commandes slash sur le serveur (via la méthode `setup_hook`).  
  Cela permet d’utiliser les commandes telles que `/favoris`, `/additem`, etc.

### Règles de la Communauté

- **Consignes d’utilisation :**  
  Veillez à respecter les règles de modération, à signaler les annonces non conformes et à configurer vos préférences de contact en fonction de vos besoins.

---

## 2. Utiliser Discord

### Vue d’ensemble des interactions

NUMMU Bot offre une riche palette d’interactions :
- **Commandes Slash et Textuelles :**  
  - `/favoris` : Gérer vos annonces favorites (ajouter, retirer, consulter en pagination).
  - `/additem` : Ajouter une annonce en fournissant une URL et d’autres informations via un formulaire (Modal).
  - `/edit_url` : Pour les modérateurs, rechercher et modifier une annonce existante.
  - `/contact_me` : Choisir de recevoir ou non des demandes de contact de la part de prospecteurs.
  - `/nummuhelp` : Afficher l’aide et la liste des commandes disponibles.
  
- **Boutons et Menus :**  
  Le bot utilise intensivement les vues interactives basées sur des boutons et des sélecteurs (menus déroulants) pour :
  - Ajouter/retirer une annonce des favoris.
  - Afficher des informations complémentaires (via le bouton "Supplément d'info.").
  - Naviguer dans des listes paginées (annonces, courtiers, acheteurs).
  - Lancer des recherches spécifiques (en géolocalisant une adresse, en filtrant par prix, etc.).

### Interaction avec les Modals

- **Formulaires de saisie :**  
  Les modals (ex. `AddItemModalSingle`, `EditBasicModal`, `EditAdvancedModal`, `ContactBuyerModal`) permettent à l’utilisateur de renseigner des informations complémentaires telles que :
  - Le prix, l’adresse, l’image, le téléphone et la description pour une annonce.
  - Les préférences de contact et les détails pour la mise en relation.

- **Validation et retour d’information :**  
  Chaque modal vérifie la validité des données saisies et enregistre les informations dans la base de données MongoDB.

---

## 3. Commandes et Outils

### /favoris

- **Description :**  
  Affiche la liste des annonces favorites de l’utilisateur sous forme paginée.  
  Vous pouvez naviguer entre les pages et accéder aux détails ou supprimer un favori via des boutons interactifs.

### /additem

- **Description :**  
  Permet d’ajouter une nouvelle annonce via son URL.  
- **Fonctionnement :**  
  - Vérifie si l’URL est déjà présente (pour éviter les doublons).
  - Si l’annonce n’existe pas, ouvre un modal de saisie pré-rempli où vous pouvez compléter le prix, l’adresse, le téléphone, etc.
  - Une fois soumis, la demande est envoyée dans le canal de modération pour validation.

### /edit_url

- **Description :**  
  Réservé aux modérateurs.  
  Recherche une annonce par URL dans l’ensemble des collections et propose des options pour modifier (édition basique ou avancée) ou supprimer l’annonce.

### /contact_me

- **Description :**  
  Permet à l’utilisateur de choisir s’il souhaite être contacté par des prospecteurs immobiliers.  
- **Fonctionnement :**  
  Affiche un embed avec deux boutons (« Me rendre joignable » et « Ne pas être contacté ») qui mettent à jour votre paramètre `trackingsearch` dans la base.

### Commandes Utilitaires

- **/nummuhelp :** Affiche l’aide détaillée du bot.  
- **/login :** Fournit le lien vers l’espace client (login).  
- **/purge, /nummupurge, cleardm :** Permettent de supprimer des messages dans un salon ou en messages privés (DM) du bot.

---

## 4. Gestion du Compte

### Inscription et Mise à Jour du Profil

- **Enregistrement automatique :**  
  Lors de votre première interaction, NUMMU vérifie si votre profil existe dans la collection MongoDB (`user_vlive_qc`).  
  Si non, un nouveau document est créé avec vos informations de base (ID Discord, pseudo, date d’inscription, etc.).

- **Mise à jour continue :**  
  À chaque interaction, vos informations (rôles, serveur, etc.) sont mises à jour via la fonction `update_discord_user_client_info`.

### Préférences de Contact

- **Paramètre `trackingsearch` :**  
  Ce paramètre détermine si vous acceptez d’être contacté par des prospecteurs.  
  La commande `/contact_me` et la vue associée vous permettent de le modifier facilement.

### Historique des Interactions

- **Enregistrement des actions :**  
  Les actions effectuées (ajout aux favoris, vues d’annonces, demandes de contact) sont enregistrées dans votre document utilisateur, ce qui permet une recherche inversée pour identifier des acheteurs potentiels.

---

## 5. Modération et Administration

### Outils de Modération

- **Commandes réservées :**  
  La commande `/edit_url` est accessible uniquement aux modérateurs et permet d’éditer ou de supprimer des annonces.
- **Vues interactives pour modération :**  
  Des vues telles que `ModSignalView` permettent aux modérateurs de gérer les signalements d’annonces en mettant à jour le statut (ex. « Vendu », « Inactif », « Doublon », « Autre »).
- **Modals d’édition :**  
  Deux niveaux d’édition sont proposés :
  - **Basique** (édition de champs essentiels comme le prix, l’adresse, l’image)
  - **Avancé** (édition du document entier en JSON brut)

### Signalements

- **Processus de signalement :**  
  Lorsqu’un utilisateur clique sur le bouton **"Signaler"**, un modal (`DetailSignalModal`) s’ouvre pour préciser la raison du signalement.  
  Le bot enregistre ensuite l’action côté utilisateur et côté annonce (pour permettre la recherche inversée).

---

## 6. Paiement et Abonnements

### Présentation des Plans

NUMMU Bot propose plusieurs plans d’abonnement :
- **FREEMIUM** – Accès de base sur le serveur public (gratuit).
- **PREMIUM** – Serveur privé, accès aux réseaux professionnels et options avancées.
- **PLATINUM** – Inclus PREMIUM avec fonctionnalités supplémentaires (conférences, support en visio, etc.).
- **PROFESSIONNEL** – Destiné aux prospecteurs, avec outils de mise en relation et diffusion multimédia.
- **SIGNATURE** – Le plan le plus complet incluant des options de streaming en direct et de pay‑per‑view.

Chaque plan (sauf FREEMIUM) dispose de liens de souscription (mensuel, annuel, etc.) et de tarifs spécifiques.

### Mise à Niveau

- **Bouton et Commande NUMMU :**  
  Le bouton **NUMMU** et la commande `/nummu` affichent votre abonnement actuel et vous proposent des options de mise à niveau via des embeds interactifs et la vue `SubscriptionPaginationView`.

---

## 7. Fonctionnalités Avancées

### Recherche Géographique et Inversée

- **Géocodage :**  
  Le bot interroge l’API Nominatim (OpenStreetMap) pour convertir une adresse en coordonnées, permettant ainsi de rechercher des annonces dans un rayon défini.
- **Recherche inversée :**  
  En analysant l’historique des interactions (favoris, vues) sur chaque annonce, NUMMU identifie des acheteurs potentiels et permet aux prospecteurs de les contacter.

### Pagination et Filtrage

- **Vues paginées :**  
  Les listes d’annonces, de courtiers et d’acheteurs sont présentées sous forme paginée avec des boutons « Précédent » et « Suivant ».
- **Filtres interactifs :**  
  Des menus déroulants (dans les vues comme `SearchView` ou `BrokersFilterView`) vous permettent de filtrer les résultats par annonceur, type de profil, rayon de recherche, langue, etc.

### Gestion des Courtiers

- **BrokersPaginationView :**  
  Une vue dédiée présente les courtiers immobiliers à proximité, avec des options pour filtrer par langue et par type de courtage.

---

## 8. Architecture et Développement

### Structure du Code

Le code de NUMMU Bot est organisé en plusieurs modules pour faciliter la maintenance et l’extension :

- **nummu_bot.py** : Fichier principal qui initialise le bot, configure les intents et synchronise les commandes.
- **Fonctions Utilitaires** : Contient les outils pour le géocodage, le nettoyage des adresses, le calcul de distances (fonction haversine), la normalisation des URL, etc.
- **Gestion de la Base de Données** : Utilisation de MongoDB via `motor` pour stocker les documents utilisateurs, annonces, signalements et historiques d’actions.
- **Vues et Modals** : Classes basées sur `discord.ui.View` et `discord.ui.Modal` pour gérer les interactions (favoris, édition, recherche, signalement, abonnements, etc.).
- **Gestion des Abonnements** : Les plans d’abonnement sont définis dans une structure (variable `SUBSCRIPTION_PLANS`), et des vues interactives permettent de naviguer dans ces options.

### Base de Données et Cache

- **MongoDB :**  
  Le bot stocke les informations utilisateur dans la collection `user_vlive_qc` et les annonces dans plusieurs collections (selon la source, par exemple `centris_full`, `kijiji_full`, etc.).
- **Caching :**  
  Un cache en mémoire (via `cachetools.TTLCache`) est utilisé pour optimiser certaines requêtes et limiter le nombre d’appels aux API externes (ex. géocodage).

### Personnalisation et Extensions

- **Modularité :**  
  Le code est conçu de façon modulaire pour vous permettre d’ajouter de nouvelles fonctionnalités ou de modifier les interactions existantes.
- **Intégration externe :**  
  Le bot interagit avec des services externes (Nominatim pour le géocodage, Discord pour les interactions, etc.) et peut être étendu pour intégrer d’autres APIs si nécessaire.

---

## Conclusion

Ce document fournit une vue d’ensemble complète des fonctionnalités de NUMMU Bot, depuis les commandes de base jusqu’aux outils avancés de modération et de paiement. Pour toute question ou pour apporter des modifications, consultez directement le code source commenté dans `nummu_bot.py` ou contactez l’équipe NUMMU.

---

*Ce guide a été généré à partir de l’analyse du code source du bot NUMMU. Pour des détails techniques supplémentaires, veuillez vous référer aux commentaires du code.*

---

## Licence

[Indiquez ici la licence du projet, par exemple MIT, Apache, etc.]

## Contact

Pour toute question ou support, contactez [Votre Nom / Équipe NUMMU] via [adresse email ou lien vers le serveur Discord].

