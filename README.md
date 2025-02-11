# Documentation de V-LIVE.radar

Bienvenue dans la documentation complète de **V-LIVE.radar**, votre outil Discord dédié à la gestion et à la mise en relation dans le secteur immobilier. Conçu pour vous simplifier l’accès aux annonces, à la recherche géographique et à la mise en relation entre acheteurs et prospecteurs, NUMMU Bot offre une multitude de fonctionnalités. Son code source complet compte plus de 7000 lignes et s’appuie sur des technologies telles que discord.py, MongoDB, et le caching pour garantir rapidité et fiabilité.

> **Remarque :** Ce guide utilisateur est conçu pour répondre aux besoins spécifiques des utilisateurs FREEMIUM, PREMIUM, PLATINUM, PROFESSIONNEL et SIGNATURE. Chaque section vous expliquera ce que vous pouvez attendre de NUMMU Bot en fonction de votre abonnement.

---

## Table des Matières

1. [Premiers Pas](#1-premiers-pas)
2. [Utiliser Discord](#2-utiliser-discord)
3. [Commandes et Outils](#3-commandes-et-outils)
4. [Gestion du Compte](#4-gestion-du-compte)
5. [Modération et Administration](#5-modération-et-administration)
6. [Paiement et Abonnements](#6-paiement-et-abonnements)
   - [Guide Utilisateur par Abonnement](#guide-utilisateur-par-abonnement)
     - [FREEMIUM](#utilisateur-freemium)
     - [PREMIUM](#utilisateur-premium)
     - [PLATINUM](#utilisateur-platinum)
     - [PROFESSIONNEL](#utilisateur-professionnel)
     - [SIGNATURE](#utilisateur-signature)
7. [Fonctionnalités Avancées](#7-fonctionnalités-avancées)
8. [Architecture et Développement](#8-architecture-et-développement)

---

## 1. Premiers Pas

<details>
  <summary><strong>Démarrage rapide</strong></summary>

  - **Invitation & Configuration :**  
    Pour commencer, invitez NUMMU Bot sur votre serveur Discord en utilisant l’URL d’invitation qui vous est communiquée en interne.  
    Ensuite, configurez les paramètres essentiels (ID du serveur, salons dédiés, rôles administratifs, etc.) en modifiant le fichier de configuration `settings.py`.

  - **Installation des Dépendances :**  
    NUMMU Bot repose sur plusieurs bibliothèques :
    - `discord.py` pour l’interaction avec Discord (commandes classiques et slash),
    - `motor` pour accéder à MongoDB de manière asynchrone,
    - `cachetools` pour le caching avec une durée de vie limitée (TTL),
    - `aiohttp`, `asyncio` et d’autres modules standards.
    
    Installez ces dépendances via pip (souvent à l’aide d’un fichier `requirements.txt` fourni).
    
</details>

<details>
  <summary><strong>Démarrage via Discord</strong></summary>

  - **Synchronisation des Commandes :**  
    Au lancement, le bot synchronise ses commandes slash sur votre serveur grâce à la méthode `setup_hook`.  
    Vous pourrez ainsi utiliser des commandes telles que `/favoris`, `/additem`, `/contact_me`, etc.
    
</details>

<details>
  <summary><strong>Règles de la Communauté</strong></summary>

  - **Consignes d’utilisation :**  
    Respectez les règles de modération en signalant toute annonce non conforme et en configurant vos préférences de contact selon vos besoins.  
    Cette démarche garantit une expérience sécurisée et agréable pour tous.
    
</details>

---

## 2. Utiliser Discord

<details>
  <summary><strong>Vue d’ensemble des interactions</strong></summary>

  NUMMU Bot vous offre une interface riche et interactive pour naviguer dans les annonces immobilières :
  
  - **Commandes Slash et Textuelles :**  
    - **`/favoris`** : Affiche et gère vos annonces favorites (ajout, suppression, pagination).
    - **`/additem`** : Permet d’ajouter une nouvelle annonce en entrant une URL et d’autres informations via un formulaire (Modal).
    - **`/edit_url`** : (Réservé aux modérateurs) Recherche et modifie une annonce existante.
    - **`/contact_me`** : Choisit si vous souhaitez être contacté par des prospecteurs.
    - **`/nummuhelp`** : Affiche l’aide et la liste complète des commandes disponibles.
    
  - **Boutons et Menus :**  
    Des vues interactives vous permettent d’ajouter ou retirer des annonces des favoris, d’accéder aux informations complémentaires, de naviguer dans des listes paginées et d’initier des recherches spécifiques (ex. géolocalisation, filtres par prix).
    
</details>

<details>
  <summary><strong>Interaction avec les Modals</strong></summary>

  - **Formulaires de saisie :**  
    Des modals vous invitent à renseigner des informations essentielles telles que le prix, l’adresse, le téléphone et la description d’une annonce.  
    Des exemples incluent les modals `AddItemModalSingle`, `EditBasicModal`, `EditAdvancedModal` et `ContactBuyerModal`.

  - **Validation et retour d’information :**  
    Chaque modal vérifie la validité de vos saisies et, en cas de succès, enregistre vos données dans la base MongoDB. Vous recevez également un retour instantané via un embed qui résume l’action effectuée.
    
</details>

---

## 3. Commandes et Outils

<details>
  <summary><strong>/favoris</strong></summary>

  - **Description :**  
    Cette commande affiche la liste paginée de vos annonces favorites.  
    Vous pouvez naviguer entre les pages, consulter les détails de chaque annonce ou la retirer de vos favoris via des boutons interactifs.

</details>

<details>
  <summary><strong>/additem</strong></summary>

  - **Description :**  
    Permet d’ajouter une annonce en saisissant son URL.  
  - **Fonctionnement :**  
    1. Le bot vérifie si l’URL n’est pas déjà enregistrée pour éviter les doublons.  
    2. Si l’annonce est nouvelle, un modal de saisie pré-rempli s’ouvre pour que vous puissiez compléter les champs tels que le prix, l’adresse, etc.  
    3. Une fois le formulaire soumis, votre demande est envoyée dans le salon de modération pour validation.

</details>

<details>
  <summary><strong>/edit_url</strong></summary>

  - **Description :**  
    Réservée aux modérateurs, cette commande permet de rechercher une annonce par URL dans l’ensemble des collections et de choisir de la modifier ou de la supprimer via des options d’édition basique ou avancée.

</details>

<details>
  <summary><strong>/contact_me</strong></summary>

  - **Description :**  
    Vous permet de choisir si vous souhaitez être contacté par des prospecteurs immobiliers.  
  - **Fonctionnement :**  
    Un embed apparaît avec deux boutons :
    - **Me rendre joignable** : Active la réception de messages de contact.
    - **Ne pas être contacté** : Désactive cette fonctionnalité.
    
    Le paramètre `trackingsearch` est mis à jour en conséquence dans votre profil.

</details>

<details>
  <summary><strong>Commandes Utilitaires</strong></summary>

  - **/nummuhelp** : Affiche l’aide détaillée et la liste complète des commandes.  
  - **/login** : Vous redirige vers l’espace client pour accéder à votre compte.  
  - **/purge, /nummupurge, cleardm** : Permettent de supprimer un nombre défini de messages, que ce soit dans un salon public ou dans vos messages privés (DM).

</details>

---

## 4. Gestion du Compte

<details>
  <summary><strong>Inscription et Mise à Jour du Profil</strong></summary>

  - **Enregistrement automatique :**  
    Lors de votre première interaction, NUMMU Bot vérifie si vous possédez déjà un profil dans la base de données (`user_vlive_qc`).  
    Si ce n’est pas le cas, un nouveau document est créé automatiquement avec vos informations de base (ID Discord, pseudo, date d’inscription, etc.).

  - **Mise à jour continue :**  
    À chaque utilisation, vos informations (rôles, serveur, etc.) sont mises à jour grâce à la fonction `update_discord_user_client_info`.  
    Ainsi, votre profil reste toujours à jour et reflète vos interactions sur Discord.

</details>

<details>
  <summary><strong>Préférences de Contact</strong></summary>

  - **Paramètre `trackingsearch` :**  
    Ce paramètre détermine si vous acceptez ou non d’être contacté par des prospecteurs immobiliers.  
    Utilisez la commande `/contact_me` pour basculer entre l’état "joignable" et "non joignable".  
    Une mise à jour immédiate est effectuée dans votre profil pour garantir la confidentialité de vos données.

</details>

<details>
  <summary><strong>Historique des Interactions</strong></summary>

  - **Enregistrement des actions :**  
    Vos actions (ajout aux favoris, consultation d’annonces, demandes de contact, etc.) sont enregistrées dans votre document utilisateur.  
    Cette fonctionnalité permet, entre autres, une recherche inversée afin d’identifier des acheteurs potentiels.

</details>

---

## 5. Modération et Administration

<details>
  <summary><strong>Outils de Modération</strong></summary>

  - **Commandes réservées :**  
    Les modérateurs disposent de la commande `/edit_url` qui leur permet de rechercher une annonce par URL et d’y apporter des modifications (édition basique ou avancée) ou de la supprimer.

  - **Vues interactives pour modération :**  
    Des vues telles que `ModSignalView` sont mises à disposition pour gérer les signalements.  
    Les modérateurs peuvent ainsi marquer une annonce comme « Vendu », « Inactif », « Doublon » ou « Autre ».

  - **Modals d’édition :**  
    Deux niveaux d’édition sont prévus :
    - **Basique** : Modification des champs essentiels (prix, adresse, image, etc.).
    - **Avancé** : Edition complète du document en format JSON, pour les modifications approfondies.

</details>

<details>
  <summary><strong>Signalements</strong></summary>

  - **Processus de signalement :**  
    Si vous constatez qu’une annonce ne respecte pas les règles de la communauté, cliquez sur le bouton **"Signaler"**.  
    Un modal (`DetailSignalModal`) vous permettra de préciser la raison du signalement (ex. Vendu, Inactif, Doublon, Autre).  
    Le signalement sera enregistré à la fois dans votre profil et dans le document de l’annonce, facilitant ainsi un suivi et une modération efficaces.

</details>

---

## 6. Paiement et Abonnements

<details>
  <summary><strong>Présentation des Plans</strong></summary>

  NUMMU Bot offre plusieurs plans d’abonnement adaptés à différents besoins :

  - **FREEMIUM**  
    - **Accès :** Sur le serveur public.
    - **Fonctionnalités :**  
      - Accès au radar en temps réel.
      - Possibilité d’utiliser les commandes de base (/favoris, /additem, etc.).
      - Certaines fonctionnalités avancées (ex. recherche spécifique, boutons interactifs restreints) sont **limitées**.
    - **Guide d’utilisation :**  
      Vous pourrez utiliser la plupart des commandes, mais certaines options (comme la recherche avancée ou le contact direct avec des prospecteurs via des boutons réservés) seront désactivées ou vous inviteront à mettre à niveau votre abonnement.

  - **PREMIUM**  
    - **Accès :** Serveur privé et accès amélioré aux fonctionnalités.
    - **Fonctionnalités supplémentaires :**  
      - Accès complet aux commandes de base ainsi qu’à des vues interactives étendues.
      - Possibilité de personnaliser vos préférences et d’accéder à un support client prioritaire via Discord.
    - **Guide d’utilisation :**  
      En tant qu’utilisateur PREMIUM, vous bénéficiez d’une interface plus riche. Par exemple, vous aurez accès aux boutons « Supplément d'info. » et « Je partage ! » dans vos messages privés. Vous pourrez également lancer des recherches avancées et bénéficier d’un suivi personnalisé.

  - **PLATINUM**  
    - **Accès :** Inclus les avantages PREMIUM avec des fonctionnalités exclusives.
    - **Fonctionnalités supplémentaires :**  
      - Participation à des conférences en ligne et support en visio.
      - Accès aux fonctionnalités de modération et aux mises en relation avancées.
    - **Guide d’utilisation :**  
      Le plan PLATINUM vous offre non seulement tous les avantages PREMIUM, mais également des outils pour interagir en direct avec des professionnels (ex. via des conférences et supports en visio). Vous pouvez ainsi obtenir des conseils personnalisés et bénéficier d’une assistance plus poussée pour vos transactions immobilières.

  - **PROFESSIONNEL**  
    - **Accès :** Conçu pour les prospecteurs.
    - **Fonctionnalités supplémentaires :**  
      - Outils de mise en relation améliorés pour contacter directement les acheteurs.
      - Diffusion multimédia et accès aux fonctionnalités de recherche inversée pour identifier des acheteurs potentiels.
    - **Guide d’utilisation :**  
      En tant qu’utilisateur PROFESSIONNEL, vous pouvez utiliser des outils exclusifs pour contacter et relancer directement des acheteurs identifiés via le système de favoris et d’historique. La commande `/contact_me` et d’autres boutons interactifs vous permettent de lancer des demandes de mise en relation, d’envoyer des messages privés personnalisés et de suivre vos contacts via un historique détaillé.

  - **SIGNATURE**  
    - **Accès :** Le plan le plus complet.
    - **Fonctionnalités supplémentaires :**  
      - Accès à des options de streaming en direct, pay‑per‑view et à la diffusion multimédia avancée.
      - Outils exclusifs pour organiser des visites virtuelles en temps réel.
    - **Guide d’utilisation :**  
      Le plan SIGNATURE est destiné aux utilisateurs souhaitant bénéficier d’une expérience immersive. Vous pouvez lancer des sessions de streaming en direct, organiser des visites virtuelles de propriétés et utiliser des outils interactifs pour engager vos prospects de manière dynamique. Ce plan est idéal pour les professionnels qui veulent une visibilité maximale et une interaction en temps réel.

</details>

<details>
  <summary><strong>Mise à Niveau</strong></summary>

  - **Bouton et Commande NUMMU :**  
    Le bouton **NUMMU** et la commande `/nummu` vous permettent de consulter votre abonnement actuel.  
    Ils vous offrent également la possibilité de mettre à niveau votre plan en affichant un embed interactif qui présente les options de souscription (mensuelle, annuelle, etc.) et les tarifs correspondants pour PREMIUM, PLATINUM, PROFESSIONNEL et SIGNATURE.
    
</details>

---

## Guide Utilisateur par Abonnement

Afin de vous aider à tirer le meilleur parti de NUMMU Bot, voici un récapitulatif des fonctionnalités et conseils d’utilisation en fonction de votre type d’abonnement :

### Utilisateur FREEMIUM

- **Accès de Base :**  
  - Vous bénéficiez d’un accès gratuit au radar et aux commandes essentielles comme `/favoris` et `/additem`.
  - Certaines fonctionnalités avancées (par exemple, les boutons « Supplément d’info. » et la recherche spécifique) sont restreintes.
  
- **Limitations :**  
  - Vous ne pouvez pas utiliser certaines commandes réservées aux abonnements supérieurs (ex. fonctionnalités de contact direct, filtres avancés dans les vues interactives).
  - Un message d’invitation à mettre à niveau apparaîtra si vous tentez d’accéder à des options premium.
  
- **Conseils :**  
  - Profitez pleinement des fonctionnalités de base pour suivre les annonces et enregistrer vos favoris.
  - Pour accéder aux outils avancés et à une expérience enrichie, pensez à passer à un abonnement supérieur.

### Utilisateur PREMIUM

- **Accès Amélioré :**  
  - En plus des fonctionnalités FREEMIUM, vous accédez à un serveur privé avec une interface enrichie.
  - Les boutons interactifs dans vos DM sont activés, vous permettant d’utiliser pleinement la fonction « Supplément d’info. » et de partager les annonces.
  
- **Avantages :**  
  - Interface plus complète et options de recherche avancée.
  - Un support client prioritaire via Discord est à votre disposition en cas de besoin.
  
- **Conseils :**  
  - Explorez les fonctionnalités de recherche avancée pour trouver des annonces plus précises.
  - Utilisez les options de partage pour diffuser vos annonces et entrer en contact avec des prospecteurs.

### Utilisateur PLATINUM

- **Accès Exclusif :**  
  - Vous bénéficiez de tous les avantages PREMIUM, plus des fonctionnalités exclusives comme l’accès à des conférences en ligne et le support en visio.
  - Vous pouvez interagir en direct avec des professionnels grâce aux outils de mise en relation avancée.
  
- **Avantages :**  
  - Réunions en visioconférence pour des conseils personnalisés.
  - Fonctionnalités de modération avancée qui vous permettent de gérer plus efficacement vos annonces.
  
- **Conseils :**  
  - Participez aux conférences et profitez du support en visio pour obtenir une assistance immédiate.
  - Exploitez les outils de mise en relation pour entrer en contact direct avec des acheteurs ou d’autres professionnels.

### Utilisateur PROFESSIONNEL

- **Fonctionnalités Avancées pour Prospecteurs :**  
  - Ce plan est dédié aux prospecteurs immobiliers. Il vous permet d’accéder à des outils de mise en relation optimisés.
  - Vous pouvez identifier les acheteurs potentiels grâce à la recherche inversée et contacter directement les prospects.
  
- **Avantages :**  
  - Outils de contact direct via les messages privés et la gestion d’historique détaillé.
  - Possibilité d’utiliser des filtres et des vues interactives avancées pour optimiser vos recherches.
  
- **Conseils :**  
  - Utilisez la commande `/contact_me` et les boutons dédiés pour lancer vos demandes de mise en relation.
  - Exploitez le système d’historique pour suivre vos interactions et améliorer votre stratégie de contact.

### Utilisateur SIGNATURE

- **Expérience Ultra-Complète :**  
  - Le plan SIGNATURE regroupe toutes les fonctionnalités des plans supérieurs et ajoute des options de streaming en direct et de pay‑per‑view.
  - Idéal pour les professionnels souhaitant organiser des visites virtuelles en temps réel et diffuser leurs annonces de manière interactive.
  
- **Avantages :**  
  - Streaming live pour des visites interactives.
  - Outils de diffusion multimédia qui vous permettent d’engager vos prospects en direct.
  
- **Conseils :**  
  - Utilisez les options de streaming pour organiser des présentations en direct de vos propriétés.
  - Profitez des outils interactifs pour créer une expérience immersive et dynamique pour vos prospects.
  - Mettez à profit la fonctionnalité de pay‑per‑view pour monétiser des sessions exclusives si vous le souhaitez.

---

## 7. Fonctionnalités Avancées

<details>
  <summary><strong>Recherche Géographique et Inversée</strong></summary>

  - **Géocodage :**  
    NUMMU Bot interroge l’API Nominatim (OpenStreetMap) pour convertir une adresse en coordonnées. Cela permet de rechercher des annonces dans un rayon défini autour de votre position ou d’une adresse donnée.
  
  - **Recherche Inversée :**  
    En analysant votre historique (favoris, vues, demandes de contact), le bot peut identifier des acheteurs potentiels et faciliter la mise en relation entre prospecteurs et acheteurs.
    
</details>

<details>
  <summary><strong>Pagination et Filtrage</strong></summary>

  - **Vues Paginées :**  
    Les listes d’annonces, de courtiers et d’acheteurs sont organisées en pages avec des boutons « Précédent » et « Suivant ».
  
  - **Filtres Interactifs :**  
    Des menus déroulants vous permettent de filtrer les résultats par annonceur, type de profil, rayon de recherche, langue, etc.
    
</details>

<details>
  <summary><strong>Gestion des Courtiers</strong></summary>

  - **BrokersPaginationView :**  
    Vous pouvez visualiser une liste paginée de courtiers immobiliers à proximité, avec des options pour filtrer par langue et par type de courtage.
    
</details>

---

## 8. Architecture et Développement

<details>
  <summary><strong>Structure du Code</strong></summary>

  Le code de NUMMU Bot est organisé de manière modulaire pour faciliter la maintenance et l’extension. Les points clés sont :
  
  - **nummu_bot.py :** Le fichier principal qui initialise le bot, configure les intents et synchronise les commandes.
  - **Fonctions Utilitaires :** Outils pour le géocodage, le nettoyage d’adresses, le calcul de distances (fonction haversine), la normalisation d’URL, etc.
  - **Gestion de la Base de Données :** Utilisation de MongoDB via `motor` pour stocker les informations des utilisateurs, les annonces et les historiques d’actions.
  - **Vues et Modals :** Diverses classes basées sur `discord.ui.View` et `discord.ui.Modal` qui gèrent les interactions (favoris, édition, signalement, mise en relation, etc.).
  - **Gestion des Abonnements :** Les plans d’abonnement sont définis dans la variable `SUBSCRIPTION_PLANS` et présentés via une vue paginée pour permettre de naviguer dans les options disponibles.
    
</details>

<details>
  <summary><strong>Base de Données et Cache</strong></summary>

  - **MongoDB :**  
    Toutes vos informations et celles relatives aux annonces sont stockées dans la collection `user_vlive_qc` (pour les utilisateurs) et dans plusieurs collections pour les annonces (ex. `centris_full`, `kijiji_full`, etc.).
  
  - **Caching :**  
    Pour optimiser certaines requêtes (comme le géocodage), un cache en mémoire via `cachetools.TTLCache` est utilisé, limitant ainsi les appels aux API externes.

</details>

<details>
  <summary><strong>Personnalisation et Extensions</strong></summary>

  - **Modularité :**  
    Le code est structuré pour faciliter l’ajout de nouvelles fonctionnalités ou la modification des interactions existantes.
  
  - **Intégration Externe :**  
    NUMMU Bot interagit avec plusieurs services externes (comme l’API Nominatim pour le géocodage ou Discord pour les interactions) et peut être étendu pour intégrer d’autres API selon vos besoins.

</details>

---

## Conclusion

Ce guide utilisateur vous offre une vue d’ensemble complète de NUMMU Bot. Vous y trouverez des instructions détaillées depuis l’installation jusqu’à l’utilisation avancée, ainsi qu’un focus sur les fonctionnalités accessibles selon votre abonnement (FREEMIUM, PREMIUM, PLATINUM, PROFESSIONNEL et SIGNATURE). Pour toute question ou pour apporter des modifications, veuillez consulter directement le code source commenté dans `nummu_bot.py` ou contacter l’équipe NUMMU.

---

*Ce guide a été généré à partir de l’analyse du code source du bot NUMMU. Pour des détails techniques supplémentaires, reportez-vous aux commentaires du code.*

---

## Licence

**MIT License** *(ou la licence de votre choix)*

---

## Contact

Pour toute question ou support, contactez :  
**[Vezuras / Équipe NUMMU]**  
Email : [info@nummu.ca]  
Ou rejoignez notre serveur Discord via le lien suivant : [Lien Discord](https://discord.gg/GZWHm9mTsu)
