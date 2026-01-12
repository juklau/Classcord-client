# ClassCord Client

##  Description

ClassCord Client est une application de messagerie instantanée développée en Java dans le cadre d'une semaine intensive. Elle permet la communication entre plusieurs utilisateurs via un serveur TCP, avec gestion des comptes, des statuts et des messages privés.

##  Fonctionnalités principales

- **Connexion au serveur** via TCP avec adresse IP et port personnalisables
- **Modes de connexion** : utilisateur authentifié ou invité
- **Messagerie** :
    - Messages globaux visibles par tous
    - Messages privés entre utilisateurs
    - Affichage en temps réel
- **Gestion des utilisateurs** :
    - Inscription et connexion sécurisées
    - Liste dynamique des utilisateurs connectés
    - Statuts personnalisables (En ligne, Absent, Invisible, Indisponible)
- **Interface graphique Swing** intuitive et moderne
- **Couleurs personnalisées** pour chaque utilisateur

##  Installation et lancement

### Prérequis

- Java 11 ou supérieur
- Maven
- Serveur ClassCord (inclus dans le dossier `code_serveur/`)

### Configuration du projet

1. **Cloner le dépôt**
   ```bash
   git clone https://github.com/juklau/Classcord-client.git
   cd classcord-client
   ```

2. **Compiler le projet**
   ```bash
   mvn compile
   ```

3. **Lancer le serveur**
   ```bash
   cd code_serveur
   python python_serveur_chat.py
   ```

4. **Lancer l'application cliente**
    - Exécuter la classe `ConnectToServeurUI` depuis votre IDE
    - Ou via Maven : `mvn exec:java`

### Utilisation

1. **Connexion au serveur**
    - Saisir l'adresse IP et le port du serveur
    - Cliquer sur "Connexion"

2. **Authentification**
    - **Mode Utilisateur** : créer un compte ou se connecter avec des identifiants existants
    - **Mode Invité** : saisir un pseudo temporaire

3. **Utilisation du chat**
    - **Messages globaux** : envoyer sans sélectionner d'utilisateur
    - **Messages privés** : sélectionner un utilisateur dans la liste, puis envoyer
    - **Changer de statut** : utiliser le menu déroulant en haut de l'interface

##  Architecture du projet

```
classcord-client/
├── src/
│   └── main/
│       └── java/
│           └── fr/
│               └── classcord/
│                   ├── controller/     # Logique métier
│                   ├── model/          # Modèles de données
│                   ├── ui/             # Interfaces graphiques
│                   └── app/            # Point d'entrée
├── code_serveur/                       # Serveur Python
└── pom.xml                             # Configuration Maven
```

### Architecture MVC

- **Model** : `ClientInvite`, `Message`, `User`, `UserColorManager`
- **View** : Interfaces Swing (`ConnectToServeurUI`, `LoginUI`, `ChatPersoUI`, etc.)
- **Controller** : `AuthController`, `ChatController`, `LoginController`, `SessionController`

##  Technologies utilisées

- **Langage** : Java 11+
- **Build** : Maven
- **Interface graphique** : Swing
- **Communication réseau** : Sockets TCP
- **Format d'échange** : JSON (bibliothèque `org.json`)
- **Serveur** : Python

##  Dépendances Maven

```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20231013</version>
</dependency>
```

##  Fonctionnalités techniques

- **Communication asynchrone** : threads dédiés pour l'écoute des messages
- **Gestion d'état** : HashMap pour le suivi des utilisateurs et statuts
- **Protocole JSON** : format standardisé pour tous les échanges
- **Interface réactive** : mise à jour dynamique via `SwingUtilities.invokeLater()`

##  Format des messages JSON

### Message global
```json
{
  "type": "message",
  "subtype": "global",
  "from": "alice",
  "to": "global",
  "content": "Bonjour à tous !",
  "timestamp": "2025-01-12T10:30:00"
}
```

### Message privé
```json
{
  "type": "message",
  "subtype": "private",
  "from": "alice",
  "to": "bob",
  "content": "Message confidentiel",
  "timestamp": "2025-01-12T10:31:00"
}
```

### Changement de statut
```json
{
  "type": "status",
  "user": "alice",
  "state": "away"
}
```

##  Auteur

**Klaudia Juhasz**

##  Licence

Projet développé dans un cadre pédagogique.

##  Résolution de problèmes

- **Problème de connexion** : vérifier que le serveur est bien lancé et que l'IP/port sont corrects
- **Messages non reçus** : vérifier la connexion réseau et les logs du serveur
- **Interface non responsive** : s'assurer que tous les traitements longs utilisent des threads séparés

##  Améliorations futures

- Tests multiclient approfondis
- Chiffrement des communications
- Historique persistant des conversations
- Notifications de bureau
- Émojis et fichiers multimédias
