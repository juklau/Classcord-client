# Manuel Utilisateur - ClassCord Client

## 📘 Bienvenue sur ClassCord !

ClassCord est une application de messagerie instantanée qui vous permet de communiquer avec d'autres utilisateurs en temps réel. Ce guide vous accompagnera dans la prise en main de l'application.

---

## 🚀 Démarrage rapide

### Étape 1 : Lancer l'application

1. Double-cliquez sur l'icône ClassCord
2. L'écran de connexion au serveur s'affiche

### Étape 2 : Se connecter au serveur

![Interface de connexion au serveur]

**Informations à saisir :**
- **Adresse IP du serveur** : L'adresse fournie par votre administrateur (ex: `192.168.1.100`)
- **Port du serveur** : Le numéro de port (ex: `5000`)

**Bouton :** Cliquez sur **"Connexion"**

**Succès** : Un message de confirmation apparaît  
**Échec** : Vérifiez l'adresse IP et le port, puis réessayez

---

## 👤 Choisir votre mode de connexion

Après la connexion au serveur, deux options s'offrent à vous :

### Option 1 : Mode Utilisateur (Recommandé)

**Avantages :**
- Accès à l’ensemble des fonctionnalités
- Conservation de l’historique *(fonctionnalité non implémentée)*
- Personnalisation du profil utilisateur *(fonctionnalité non implémentée)*

**Cliquez sur** : **"Mode Connexion"**

### Option 2 : Mode Invité

**Avantages :**
- Connexion rapide sans compte
- Idéal pour tester l'application

**Limitations :**
- Pas d'historique sauvegardé
- Fonctionnalités limitées

**Cliquez sur** : **"Mode Invité"**

---

## 🔐 Authentification (Mode Utilisateur)

### Première utilisation - Créer un compte

1. **Nom d'utilisateur** : Choisissez votre pseudo (ex: `alice_dupont`)
2. **Mot de passe** : Créez un mot de passe sécurisé
3. Cliquez sur **"S'inscrire"**

 **Compte créé** : Vous êtes automatiquement connecté !

### Utilisateur existant - Se connecter

1. **Nom d'utilisateur** : Saisissez votre pseudo
2. **Mot de passe** : Entrez votre mot de passe
3. Cliquez sur **"Se connecter"**

💡 **Astuce** : L'application se souvient de votre dernier nom d'utilisateur utilisé.

---

## 💬 Utiliser la messagerie

### Interface principale

L'interface de chat se compose de 4 zones principales :

```
┌─────────────────────────────────────────────┐
│  Statut :          [En ligne ▼]             │  ← Zone de statut
├─────────────────────────────┬───────────────┤
│                             │               │
│   Zone de messages          │  Liste des    │
│                             │  utilisateurs │
│                             │  connectés    │
│                             │               │
├─────────────────────────────┴───────────────┤
│  [Tapez votre message...]        [Envoyer]  │  ← Zone de saisie
└─────────────────────────────────────────────┘
```

---

## ✉️ Envoyer des messages

### Message global (visible par tous)

1. **Ne sélectionnez aucun utilisateur** dans la liste de droite
2. Tapez votre message dans le champ de saisie
3. Cliquez sur **"Envoyer"** ou appuyez sur **Entrée**

**Affichage** : `Votre_pseudo : Votre message`

### Message privé (visible uniquement par le destinataire)

1. **Cliquez sur un utilisateur** dans la liste de droite
2. Tapez votre message
3. Cliquez sur **"Envoyer"** ou appuyez sur **Entrée**

**Affichage** : `**[MP à Destinataire]** Votre message`

💡 **Astuce** : Le bouton **"↩ Global"** apparaît quand un utilisateur est sélectionné. Cliquez dessus pour repasser en mode global.

---

## 🎨 Comprendre les couleurs

### Couleurs des utilisateurs

Chaque utilisateur a une **couleur unique** qui lui est attribuée automatiquement :
- Facilite l'identification dans la conversation
- Améliore la lisibilité

### Indicateurs de statut

À côté de chaque nom d'utilisateur, un **point coloré** indique son statut :

- 🟢 **Vert** : En ligne (disponible)
- 🟠 **Orange** : Absent (AFK - Away From Keyboard)
- 🔴 **Rouge** : Indisponible (Ne pas déranger)
- ⚪ **Gris** : Invisible

---

## ⚙️ Gérer votre statut

### Changer votre statut

1. Cliquez sur le **menu déroulant en haut** de l'interface
2. Sélectionnez votre statut :
    - **En ligne** : Vous êtes disponible
    - **Absent** : Vous êtes temporairement absent
    - **Indisponible** : Vous ne souhaitez pas être dérangé
    - **Invisible** : Vous apparaissez hors ligne pour les autres

**Note** : En mode invisible, vous pouvez toujours voir et envoyer des messages, mais les autres vous voient comme déconnecté.

---

## 👥 Liste des utilisateurs connectés

### Fonctionnalités

La liste de droite affiche :
- **Tous les utilisateurs en ligne** en temps réel
- Leur **statut actuel** (point coloré)
- **Votre propre nom** apparaît également dans la liste

### Interactions

- **Clic simple** : Sélectionner pour envoyer un message privé
- **Aucune sélection** : Les messages sont envoyés en global

---

## 📨 Comprendre les types de messages

### Messages globaux

```
alice : Bonjour à tous !
bob : Salut Alice !
```

→ Visibles par **tous les utilisateurs connectés**

### Messages privés que vous envoyez

```
**[MP à bob]** Comment vas-tu ?
```

→ Visible uniquement par **vous et bob**

### Messages privés que vous recevez

```
**[MP de alice]** Très bien, merci !
```

→ Visible uniquement par **vous et alice**

---

## 🔧 Résolution des problèmes courants

### Je ne peux pas me connecter au serveur

**Solutions** :
1. Vérifiez que l'adresse IP et le port sont corrects
2. Assurez-vous que le serveur est en ligne
3. Vérifiez votre connexion Internet
4. Contactez votre administrateur réseau

### Mes messages ne s'envoient pas

**Solutions** :
1. Vérifiez que vous êtes toujours connecté
2. Regardez si le champ de saisie est vide
3. Essayez de vous reconnecter au serveur

### Je ne vois pas les autres utilisateurs

**Solutions** :
1. Attendez quelques secondes (mise à jour automatique)
2. Vérifiez votre connexion au serveur
3. Essayez de changer votre statut pour forcer une mise à jour

### J'ai oublié mon mot de passe

**Solution** :
Contactez votre administrateur pour réinitialiser votre compte.

### L'application se fige

**Solutions** :
1. Attendez quelques secondes (traitement en cours)
2. Fermez et relancez l'application
3. Vérifiez que votre ordinateur a suffisamment de mémoire disponible

---

## 💡 Bonnes pratiques

### Communication

**À FAIRE** :
- Soyez respectueux envers les autres utilisateurs
- Utilisez les messages privés pour les conversations personnelles
- Mettez à jour votre statut selon votre disponibilité
- Relisez vos messages avant de les envoyer

**À ÉVITER** :
- Envoyer des messages en MAJUSCULES (considéré comme crier)
- Spammer plusieurs messages identiques
- Partager des informations personnelles sensibles
- Utiliser le chat pour du harcèlement

### Sécurité

**Conseils de sécurité** :
- Ne partagez jamais votre mot de passe
- Choisissez un mot de passe fort (minimum 8 caractères)
- Déconnectez-vous après utilisation sur un ordinateur partagé
- Ne cliquez pas sur des liens suspects dans les messages

---

## 🆘 Support et assistance

### Besoin d'aide ?

- **Documentation** : Consultez ce manuel utilisateur
- **Administrateur** : Contactez votre administrateur système
- **Problème technique** : Notez le message d'erreur et contactez le support

### Signaler un bug

Si vous rencontrez un problème technique :
1. Notez ce que vous faisiez quand le bug est survenu
2. Prenez une capture d'écran si possible
3. Contactez votre administrateur avec ces informations

---

## 📚 Glossaire

| Terme       | Définition                                                                   |
|-------------|------------------------------------------------------------------------------|
| **Serveur** | Ordinateur central qui gère les connexions et les messages                   |
| **IP**      | Adresse Internet Protocol, identifiant unique d'un ordinateur sur le réseau  |
| **Port**    | Numéro de canal de communication sur le serveur                              |
| **MP**      | Message Privé, visible uniquement par l'expéditeur et le destinataire        |
| **Pseudo**  | Nom d'utilisateur visible par les autres                                     |
| **Statut**  | Indicateur de votre disponibilité                                            |
| **AFK**     | Away From Keyboard (absent du clavier)                                       |
| **Global**  | Message visible par tous les utilisateurs connectés                          |

---

## 📖 Tutoriel pas à pas - Premier message

### Scénario : Envoyer votre premier message global

1.  Lancez ClassCord
2.  Connectez-vous au serveur (IP + Port)
3.  Choisissez "Mode Connexion" ou "Mode Invité"
4.  Si utilisateur : Inscrivez-vous ou connectez-vous
5.  Vous êtes maintenant dans l'interface de chat !
6.  Tapez "Bonjour à tous !" dans le champ en bas
7.  Cliquez sur "Envoyer" ou appuyez sur Entrée
8.  Votre message apparaît dans la zone de discussion !

### Scénario : Envoyer votre premier message privé

1. Dans la liste de droite, repérez un utilisateur connecté
2. Cliquez sur son nom (il devient surligné)
3. Le bouton "Global" change en "↩ Global"
4. Tapez votre message privé
5. Cliquez sur "Envoyer"
6. Votre message apparaît avec le préfixe **[MP à nom_utilisateur]**

---

## 🎓 Astuces avancées

### Maximiser votre productivité

1. **Utilisez les statuts intelligemment**
    - "Indisponible" pendant les réunions importantes
    - "Absent" pendant les pauses
    - "Invisible" pour consulter sans être vu

2. **Organisez vos conversations**
    - Messages globaux pour les annonces générales
    - Messages privés pour les discussions one-to-one

3. **Repérez rapidement les nouveaux messages**
    - Les nouveaux messages apparaissent en bas de la zone de chat
    - Le défilement est automatique vers le dernier message

---

## ✨ Fonctionnalités à venir

Le projet ClassCord est en développement continu. Voici les fonctionnalités prévues :

- 😊 Support des emojis
- 📝 Historique des conversations
- 🔔 Notifications de bureau
- 🎨 Thèmes personnalisables
- 👥 Salles de discussion thématiques

---

**Version du manuel** : 1.0  
**Dernière mise à jour** : Janvier 2025

---

**Bon chat sur ClassCord !**
