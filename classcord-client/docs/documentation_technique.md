# Documentation Technique - ClassCord Client

**Version** : 2.0 | **Date** : Janvier 2025 | **Auteur** : Klaudia Juhasz

---

## 📋 Table des matières

1. [Vue d'ensemble](#vue-densemble)
2. [Architecture](#architecture)
3. [Configuration](#configuration)
4. [Structure du projet](#structure-du-projet)
5. [Composants clés](#composants-clés)
6. [Protocole de communication](#protocole-de-communication)
7. [Déploiement](#déploiement)

---

## Vue d'ensemble

### Description

ClassCord Client est une application de messagerie instantanée en Java utilisant :
- **Sockets TCP** pour la communication réseau
- **JSON** pour l'échange de données
- **Swing** pour l'interface graphique
- **Architecture MVC** avec contrôleurs dédiés

### Technologies

| Technologie | Version    | Usage                     |
|-------------|------------|---------------------------|
| Java        | 11 ou plus | Langage principal         |
| Maven       | 3.x        | Build et dépendances      |
| org.json    | 20231013   | Parsing JSON              |
| JUnit       | 5.11.0     | Tests unitaires           |
| Swing       | JDK 17     | Interface graphique       |

### Architecture globale

```
┌──────────────────┐         TCP/JSON          ┌──────────────────┐
│                  │ ◄──────────────────────►  │                  │
│  Client Java     │                           │  Serveur Python  │
│  (Swing UI)      │                           │                  │
└──────────────────┘                           └──────────────────┘
        │
        │ MVC Pattern
        ▼
┌──────────────────────────────────────────────────────────────┐
│  Model (Données) + View (UI) + Controller (Logique métier)   │
└──────────────────────────────────────────────────────────────┘
```

---

## Architecture

### Pattern MVC

```
┌─────────────────────────────────────────────────┐
│                   CONTROLLER                    │
│  AuthController, LoginController,               │
│  ChatController, SessionController              │
└────────────┬────────────────────────┬───────────┘
             │                        │
             ▼                        ▼
┌────────────────────┐    ┌──────────────────────┐
│       MODEL        │    │         VIEW         │
│  ClientInvite      │    │  ConnectToServeurUI  │
│  Message           │    │  LoginUI, GuestUI    │
│  User              │    │  ChatPersoUI         │
│  UserColorManager  │    │                      │
└────────────────────┘    └──────────────────────┘
```

### Responsabilités

| Couche         | Classes                                       | Rôle                                |
|----------------|-----------------------------------------------|-------------------------------------|
| **Model**      | `ClientInvite`, `Message`, `User`             | Données et logique métier           |
| **View**       | `LoginUI`, `ChatPersoUI`, `GuestUI`, etc.     | Interfaces graphiques               |
| **Controller** | `ChatController`, `LoginController`, etc.     | Orchestration et logique applicative|

---

## Configuration

### Prérequis

```bash
java -version  # Java 11+
mvn -version   # Maven 3.x
```

### Installation rapide

```bash
# 1. Cloner le projet
git clone https://github.com/juklau/Classcord-client.git
cd classcord-client

# 2. Compiler
mvn clean compile

# 3. Lancer
mvn exec:java -Dexec.mainClass="fr.classcord.ui.ConnectToServeurUI"
```

### Configuration Maven (pom.xml)

**Dépendances principales** :
```xml
<dependencies>
    <!-- JSON Processing -->
    <dependency>
        <groupId>org.json</groupId>
        <artifactId>json</artifactId>
        <version>20231013</version>
    </dependency>
    
    <!-- Tests JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Configuration Java 17** :
```xml
<properties>
    <maven.compiler.release>17</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

---

## Structure du projet

```
classcord-client/
├── src/main/java/fr/classcord/
│   ├── controller/          # Logique métier
│   │   ├── AuthController.java
│   │   ├── ChatController.java
│   │   ├── LoginController.java
│   │   └── SessionController.java
│   │
│   ├── model/               # Données
│   │   ├── ClientInvite.java
│   │   ├── Message.java
│   │   ├── User.java
│   │   └── UserColorManager.java
│   │
│   └── ui/                  # Interfaces
│       ├── ConnectToServeurUI.java
│       ├── LoginUI.java
│       ├── GuestUI.java
│       ├── ChatPersoUI.java
│       └── UserStatusRenderer.java
│
├── code_serveur/            # Serveur Python
├── docs/                    # Documentation du projet
├── image/                   # Screenshots
└── pom.xml                  # Configuration Maven
```

---

## Composants clés

### Model

#### `ClientInvite`
**Responsabilité** : Gestion de la connexion réseau

**Attributs principaux** :
- `socket`, `writer`, `reader` : Communication TCP
- `controller` : Délégation du traitement
- `userStatusMap` : Liste des utilisateurs connectés

**Méthodes clés** :
- `connect(ip, port)` : Connexion au serveur
- `listenForMessages()` : Écoute asynchrone des messages
- `send(message)` : Envoi de messages JSON
- `notifyOnlineStatus()` : Signale le statut au serveur

#### `Message`
**Responsabilité** : Représentation d'un message

**Attributs** : `type`, `subtype`, `from`, `to`, `content`, `timestamp`

**Méthodes** :
- `toJson()` : Conversion en JSON
- `fromJson(jsonString)` : Création depuis JSON

#### `User`
**Responsabilité** : Représentation d'un utilisateur

**Attributs** : `username`, `status`

---

### Controller

#### `SessionController`
**Responsabilité** : Navigation entre interfaces

**Méthodes clés** :
- `connecterAuServeur(ip, port, frame)` : Connexion TCP
- `connecterAuChat(pseudo, frame)` : Mode invité
- `ouvrirLoginUI()` / `ouvrirFenetreGuestUI()` : Navigation

#### `AuthController`
**Responsabilité** : Authentification (méthodes statiques)

**Méthodes** :
- `login(clientInvite, username, password)` : Connexion
- `register(clientInvite, username, password)` : Inscription

#### `LoginController`
**Responsabilité** : Orchestration de l'authentification

**Méthodes** :
- `login(username, password, frame, onSuccess, onFailure)` : Login avec callbacks
- `registerThenLogin(...)` : Inscription puis connexion automatique

#### `ChatController`
**Responsabilité** : Gestion du chat

**Méthodes clés** :
- `sendMessage(text, selectedUser)` : Envoi message global/privé
- `envoyerStatut(selection)` : Changement de statut
- `handleIncomingMessage(json)` : Traitement des messages reçus
- `afficherDernierMessage()` : Affichage dans l'UI

---

### View (UI)

#### `ConnectToServeurUI`
Connexion initiale au serveur (IP + port)

#### `ChoixModeUI`
Choix entre mode Utilisateur ou Invité

#### `LoginUI`
Authentification (inscription/connexion)

#### `GuestUI`
Connexion rapide en mode invité

#### `ChatPersoUI`
Interface principale de chat avec :
- Zone de messages formatés
- Liste des utilisateurs connectés
- Sélecteur de statut
- Envoi de messages globaux/privés

---

## Protocole de communication

### Format JSON

Tous les échanges utilisent JSON.

### Types de messages

#### Message global
```json
{
  "type": "message",
  "subtype": "global",
  "from": "alice",
  "to": "global",
  "content": "Bonjour !"
}
```

#### Message privé
```json
{
  "type": "message",
  "subtype": "private",
  "from": "alice",
  "to": "bob",
  "content": "Salut Bob"
}
```

#### Authentification
```json
// Inscription
{
  "type": "register",
  "username": "alice",
  "password": "motdepasse"
}

// Connexion
{
  "type": "login",
  "username": "alice",
  "password": "motdepasse"
}

// Réponse serveur
{
  "type": "login",
  "status": "ok",
  "message": "Connexion réussie"
}
```

#### Changement de statut
```json
{
  "type": "status",
  "user": "alice",
  "state": "away"  // online, away, dnd, invisible
}
```

#### Liste des utilisateurs
```json
{
  "type": "users",
  "users": [
    {"user": "alice", "state": "online"},
    {"user": "bob", "state": "away"}
  ]
}
```

---

## Flux de données

### Démarrage de l'application
```
ConnectToServeurUI (IP/Port)
    ↓
ClientInvite.connect()
    ↓
ChoixModeUI (Utilisateur/Invité)
    ↓
LoginUI OU GuestUI
    ↓
ChatPersoUI
```

### Envoi d'un message
```
ChatPersoUI
    ↓
ChatController.sendMessage()
    ↓
ClientInvite.send(JSON)
    ↓
Serveur
```

### Réception d'un message
```
Serveur
    ↓
ClientInvite.listenForMessages() [Thread]
    ↓
ChatController.handleIncomingMessage()
    ↓
ChatController.afficherDernierMessage()
    ↓
ChatPersoUI.appendFormattedMessage()
```

---

## Interface utilisateur (Swing)

### Bonnes pratiques Swing

**1. Thread EDT (Event Dispatch Thread)**

Toutes les modifications d'UI doivent se faire sur l'EDT :
```java
SwingUtilities.invokeLater(() -> {
    userListModel.addElement(username);
    chatArea.append(message + "\n");
});
```

**2. Gestion des événements**

```java
// Bouton
button.addActionListener(e -> sendMessage());

// Touche Entrée
inputField.addActionListener(e -> sendMessage());
```

---

## Threading et concurrence

### Thread d'écoute des messages

```java
public void listenForMessages() {
    new Thread(() -> {
        try {
            String line;
            while ((line = reader.readLine()) != null) {
                processMessage(line);
            }
        } catch (IOException e) {
            System.err.println("Erreur d'écoute : " + e.getMessage());
        }
    }).start();
}
```

### Thread pour l'authentification

```java
new Thread(() -> {
    try {
        // Opération longue (réseau)
        boolean success = AuthController.login(...);
        
        // Mise à jour de l'UI sur l'EDT
        SwingUtilities.invokeLater(() -> {
            if (success) {
                showSuccessMessage();
            } else {
                showErrorMessage();
            }
        });
    } catch (Exception e) {
        handleError(e);
    }
}).start();
```

---

## Gestion des erreurs

### Erreurs réseau
```java
try {
    socket = new Socket(ip, port);
} catch (IOException e) {
    System.err.println("Erreur de connexion : " + e.getMessage());
    closeConnection();
}
```
---

## Tests et débogage

### Logs de débogage

```java
System.out.println("=== État de la connexion ===");
System.out.println("Socket fermé ? " + socket.isClosed());
System.out.println("Pseudo : " + pseudo);
System.out.println("Utilisateurs : " + userStatusMap.keySet());
System.out.println("============================");
```

---

## Déploiement

### Création d'un JAR exécutable

**1. Ajouter dans pom.xml** :
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.4</version>
            <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>fr.classcord.ui.ConnectToServeurUI</mainClass>
                    </transformer>
                </transformers>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**2. Générer le JAR** :
```bash
mvn clean package
```

**3. Exécuter** :
```bash
java -jar target/classcord-client-1.0-SNAPSHOT.jar
```

---

## Maintenance

### Fonctionnalités futures

**Court terme** :
- Historique des conversations
- Support des emojis
- Notifications sonores

**Moyen terme** :
- Partage de fichiers
- Avatars utilisateurs
- Salles de discussion thématiques

**Long terme** :
- Chiffrement end-to-end
- Appels vocaux/vidéo

### Optimisations possibles

- Cache des couleurs utilisateurs
- Pagination de l'historique
- Compression des messages JSON
- Reconnexion automatique

---

## Annexes

## Ressources

**Documentation Java** :
- [Java SE 11 Documentation](https://docs.oracle.com/en/java/javase/11/)
- [Swing Tutorial](https://docs.oracle.com/javase/tutorial/uiswing/)

**Bibliothèques** :
- [org.json Documentation](https://stleary.github.io/JSON-java/)
- [Maven Documentation](https://maven.apache.org/guides/)


**Projet** :
- Repository : https://github.com/juklau/Classcord-client
- Auteur : Klaudia Juhasz

---

**Fin de la documentation** | Version 2.0 | Janvier 2025
