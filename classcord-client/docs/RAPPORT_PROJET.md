# Rapport de Projet ClassCord Client

## Informations Personnelles
**Développeur** : Klaudia Juhasz

---

## 📖 Jour 1 - Mise en place du projet et modélisation

### Objectifs de la journée
Configuration de l'environnement de développement, création de la structure du projet et implémentation des classes de base.

### Fonctionnalités développées

#### Configuration de l'environnement
- Installation de Java 11+
- Installation de Maven et configuration des variables d'environnement
- Installation des extensions Java et Maven dans VSCode
- Installation d'IntelliJ IDEA (août 2025)

#### Création du projet Maven
- Fork du dépôt GitHub original
- Clonage du projet en local :
  ```bash
  git clone https://github.com/juklau/Classcord-client.git
  cd classcord-client
  ```

#### Configuration du fichier `pom.xml`
Ajout de la dépendance JSON :
```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20231013</version>
</dependency>
```
Rechargement du projet Maven avec `MAJ+ALT+U`

#### Structure des packages
Création de l'architecture selon le modèle MVC :
```
classcord-client/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── fr/
│   │   │   │   ├── classcord/
│   │   │   │   │   ├── model/
│   │   │   │   │   │   ├── User.java
│   │   │   │   │   │   ├── Message.java
│   │   │   │   │   ├── network/
│   │   │   │   │   ├── ui/
│   │   │   │   │   ├── app/
│   │   │   │   │   │   ├── Main.java
├── pom.xml
```

#### Implémentation des classes métier

**Classe `User`**
- Attributs : `username`, `status`
- Constructeurs
- Getters et setters
- Méthode `toString()` pour l'affichage

**Classe `Message`**
- Attributs : `type`, `subtype`, `from`, `to`, `content`, `timestamp`
- Constructeurs
- Getters et setters
- Méthode `toString()` pour l'affichage

#### Tests et validation
- Compilation réussie avec `mvn compile`
- Test basique dans `Main.java` : `System.out.println("Hello ClassCord")`

### Résultat de la journée
-  Projet Maven fonctionnel dans VSCode  
-  Fichier `pom.xml` configuré avec la dépendance JSON  
-  Packages Java créés et classes `User` et `Message` opérationnelles  
 - Compilation sans erreur

---

## 📖 Jour 2 - Connexion au serveur et tchat en mode invité

### Objectifs de la journée
Permettre à un utilisateur de se connecter au serveur en tant qu'invité et d'échanger des messages globaux.

### Fonctionnalités développées

#### Architecture mise en place
- **Package `fr.classcord.controller`** → Création de la classe `ClientInvite`
- **Package `fr.classcord.model`** → Gestion des messages JSON
- **Package `fr.classcord.ui`** → Interface Swing `ChatInterface`

#### Classe `ClientInvite` - Connexion au serveur

**Méthode `connect(String ip, int port)`**
```java
public boolean connect(String ip, int port) {
    try {
        // Création d'une connexion TCP entre client et serveur
        socket = new Socket(ip, port);
        
        // Envoyer des données au serveur
        writer = new PrintWriter(socket.getOutputStream(), true);
        
        // Lire les messages envoyés par le serveur
        reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        
        System.out.println("Connecté au serveur " + ip + " : " + port);
        return true;
    } catch (IOException e) {
        System.out.println("Problème pendant la connexion au serveur : " + e.getMessage());
        // Fermeture de la connexion en cas d'échec
        if (socket != null) {
            try {
                socket.close();
            } catch (IOException closeException) {
                System.out.println("Erreur pendant la fermeture de la connexion " + closeException);
            }
        }
        return false;
    }
}
```

**Méthode `sendMessage(String messageText)` - Envoi de messages**
```java
public void sendMessage(String messageText) {
    if (pseudo == null || pseudo.isEmpty()) {
        System.err.println("Erreur : pseudo non défini.");
        return;
    }
    
    if (writer != null && socket != null && !socket.isClosed()) {
        Message message = new Message("message", "global", pseudo, "global", messageText, "");
        writer.println(message.toJson().toString());
    } else {
        System.err.println("Impossible d'envoyer le message, la connexion est fermée.\n");
    }
}
```

Format JSON du message :
```json
{
  "type": "message",
  "subtype": "global",
  "from": "pseudo",
  "to": "global",
  "content": "Contenu du message",
  "timestamp": ""
}
```

#### Réception asynchrone des messages

**Méthode `listenForMessages()`**
Création d'un thread secondaire pour écouter les messages en continu :
```java
new Thread(() -> {
    try {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println("Message reçu : " + line);
        }
    } catch (IOException e) {
        System.err.println("Erreur de réception");
    }
}).start();
```

#### Interface graphique Swing - `ChatInterface`

**Composants créés**
```java
pseudo = new JTextField(10);
adresseIP = new JTextField(10);
adressePort = new JTextField(10);
```

**Gestion de la connexion**
```java
btnConnexion.addActionListener(e -> btnConnexion_clic());
```

**Envoi de messages**
```java
private void sendMessage() {
    if (clientInvite != null) {
        String messageText = inputField.getText().trim();
        if (!messageText.isEmpty()) {
            clientInvite.sendMessage(messageText);
            chatArea.append("Vous : " + messageText + "\n");
            inputField.setText("");
        }
    } else {
        chatArea.append("Erreur : Vous devez être connecté avant d'envoyer un message.\n");
    }
}
```

Gestion de l'événement du bouton :
```java
sendButton.addActionListener(e -> sendMessage());
```

#### Encapsulation de la logique JSON dans la classe `Message`

**Méthode `toJson()`**
```java
public JSONObject toJson() {
    JSONObject json = new JSONObject();
    json.put("type", type);
    json.put("subtype", subtype);
    json.put("from", from);
    json.put("to", to);
    json.put("content", content);
    json.put("timestamp", timestamp);
    return json;
}
```

**Méthode `fromJson(String jsonString)`**
```java
public static Message fromJson(String jsonString) {
    JSONObject json = new JSONObject(jsonString);
    return new Message(
        json.getString("type"),
        json.optString("subtype", "global"),
        json.getString("from"),
        json.optString("to", "global"),
        json.getString("content"),
        json.getString("timestamp")
    );
}
```

### Résultat de la journée
-  Classe `ClientInvite` fonctionnelle avec communication serveur  
-  Envoi et réception de messages en mode invité  
-  Interface Swing opérationnelle pour le chat  
-  Encapsulation JSON dans la classe `Message`  
-  Interface graphique de base fonctionnelle

---

## 📖 Jour 3 - Authentification et gestion des comptes utilisateurs

### Objectifs de la journée
Implémenter l'inscription et la connexion avec gestion des identifiants utilisateur.

### Fonctionnalités développées

#### Architecture enrichie
- **Package `fr.classcord.model`** → Ajout de la classe `CurrentUser`
- **Package `fr.classcord.ui`** → Ajout des classes :
    - `ConnectToServeurUI`
    - `ChoixModeUI`
    - `LoginUI`
    - `ChatInterfacePerso`
    - `GuestUI`

#### Interface de connexion au serveur - `ConnectToServeurUI`

**Méthode `connectToServer()`**
```java
private void connectToServer() {
    String ip = adresseIPServeur.getText().trim();
    int port;
    
    try {
        port = Integer.parseInt(adressePortServeur.getText().trim());
    } catch (NumberFormatException e) {
        JOptionPane.showMessageDialog(this, "Le port doit être un nombre valide !");
        return;
    }
    
    if (!ip.isEmpty()) {
        clientInvite = new ClientInvite("invité");
        boolean connected = clientInvite.connect(ip, port);
        
        if (connected) {
            JOptionPane.showMessageDialog(this, "Connexion réussie au serveur " + ip + " : " + port);
            dispose();
            
            if (clientInvite != null) {
                SwingUtilities.invokeLater(() -> new ChoixModeUI(clientInvite).setVisible(true));
            } else {
                System.err.println("Erreur : clientInvite est null !");
            }
        } else {
            JOptionPane.showMessageDialog(this, "Erreur de connexion au serveur.");
        }
    } else {
        JOptionPane.showMessageDialog(this, "Veuillez entrer une adresse IP valide !");
    }
}
```

#### Interface de choix du mode - `ChoixModeUI`

Permet à l'utilisateur de choisir entre :
- Mode Invité
- Mode Utilisateur (avec authentification)

#### Interface d'authentification - `LoginUI`

**Composants**
```java
usernameField = new JTextField();
passwordField = new JPasswordField(); // Masquage du mot de passe
```

**Boutons** : "Se connecter" / "S'inscrire"

**Deux scénarios principaux**

1. **Inscription** : méthode `loginApresRegistration()`
    - Création d'un thread pour l'inscription
    - Appel de `register()` puis `login()` de la classe `User`
    - Utilisation de `SwingUtilities.invokeLater()` pour la mise à jour de l'interface

2. **Connexion directe** : méthode `authenticateUser()`
    - Création d'un thread pour l'authentification
    - Appel de `login()` de la classe `User`
    - Utilisation de `SwingUtilities.invokeLater()`

#### Communication avec le serveur

**Format JSON pour l'inscription**
```json
{
  "type": "register",
  "username": "alice",
  "password": "azerty"
}
```

**Format JSON pour la connexion**
```json
{
  "type": "login",
  "username": "alice",
  "password": "azerty"
}
```

#### Gestion des réponses du serveur

**En cas de succès**
```java
JOptionPane.showMessageDialog(this, "Bienvenue " + pseudo + " !");
// Transition vers l'interface de chat
```

**En cas d'échec**
```java
JOptionPane.showMessageDialog(this, "Erreur : " + e.getMessage());
```

#### Interface invité - `GuestUI`

**Méthode `btnConnexionChatClic()`**
Permet la connexion en mode invité avec un simple pseudo.

#### Fonctionnalités bonus

**Mémorisation du dernier pseudo**
```java
String lastUser = readLastUsername();
if (!lastUser.isEmpty()) {
    usernameField.setText(lastUser);
}
```

**Indicateur de chargement**
(ne fonctionne pas actuellement)
```java
// Pendant la connexion
loaderLabel.setVisible(true);

// Après connexion
loaderLabel.setVisible(false);
```

Icône téléchargée depuis [Pixabay](https://pixabay.com/fr/gifs/) : `spinner-loading`

### Résultat de la journée
- Fenêtre Swing d'inscription/connexion opérationnelle  
-  Envoi des identifiants via socket avec format JSON  
-  Affichage des messages de succès ou d'erreur  
-  Accès automatique à la fenêtre de tchat après connexion  
-  Navigation fluide entre fenêtres  
-  Respect du protocole JSON  
-  Interface graphique claire et intuitive  
-  Séparation des responsabilités (UI / logique / réseau)

---

## 📖 Jour 4 - Messages privés et liste des utilisateurs connectés

### Objectifs de la journée
Implémenter la messagerie privée et afficher dynamiquement la liste des utilisateurs.

### Fonctionnalités développées

#### Affichage de la liste des utilisateurs connectés

**Dans `ChatInterface` et `ChatInterfacePerso`**
```java
private final DefaultListModel<String> userListModel = new DefaultListModel<>();
private final JList<String> userList = new JList<>(userListModel);
```

#### Gestion des messages de statut

**Dans la méthode `listenForMessages()` de `ClientInvite`**

**Interception des messages de type "status"**
```java
case "status" -> {
    String username = json.optString("user");
    String statut = json.optString("state");
    
    userStatusMap.put(username, statut);
    
    if (chatInterfacePerso != null) {
        chatInterfacePerso.updateUserList(new HashMap<>(userStatusMap));
    } else if (chatInterface != null) {
        chatInterface.updateUserList(new HashMap<>(userStatusMap));
    }
}
```

Format JSON :
```json
{
  "type": "status",
  "user": "bob",
  "state": "online"
}
```

**Interception des messages de type "users"**
```java
case "users" -> {
    System.out.println("Liste complète reçue");
    JSONArray usersArray = json.optJSONArray("users");
    
    if (usersArray != null) {
        userStatusMap.clear();
        
        for (int i = 0; i < usersArray.length(); i++) {
            String pseudo = usersArray.optString(i);
            userStatusMap.put(pseudo, "online");
        }
        
        if (chatInterfacePerso != null) {
            chatInterfacePerso.updateUserList(new HashMap<>(userStatusMap));
        } else if (chatInterface != null) {
            chatInterface.updateUserList(new HashMap<>(userStatusMap));
        }
    } else {
        System.err.println("Réponse 'users' invalide : pas de champ 'users'");
    }
}
```

#### Mise à jour dynamique de la liste

**Méthode `updateUserList(Map<String, String> userMap)` dans `ChatInterfacePerso`**
```java
public void updateUserList(Map<String, String> userMap) {
    SwingUtilities.invokeLater(() -> {
        userListModel.clear();
        System.out.println("🧾 Mise à jour de la liste d'utilisateurs connectés :");
        
        for (Map.Entry<String, String> entry : userMap.entrySet()) {
            String pseudo = entry.getKey();
            String statut = entry.getValue();
            
            if ("online".equalsIgnoreCase(statut)) {
                userListModel.addElement(pseudo);
                System.out.println("Résultat : " + pseudo + " est en ligne.");
            }
        }
        
        // Ajouter le pseudo local s'il n'est pas déjà dans la liste
        String localUser = clientInvite.getPseudo();
        if (localUser != null && !localUser.isEmpty() && !userListModel.contains(localUser)) {
            userListModel.addElement(localUser);
            System.out.println("Ajout de l'utilisateur local : " + localUser);
        }
    });
}
```

#### Envoi de messages privés

**Modification de la méthode `sendMessage()` dans `ChatInterfacePerso`**
```java
String selectedUser = userList.getSelectedValue();

JSONObject json = new JSONObject();
json.put("type", "message");
json.put("content", messageText);

if (selectedUser != null && !selectedUser.isEmpty()) {
    // Envoyer un message privé
    json.put("subtype", "private");
    json.put("to", selectedUser);
    chatArea.append("**[MP à " + selectedUser + "]** " + messageText + "\n");
} else {
    // Envoyer un message global
    json.put("subtype", "global");
    json.put("to", "global");
    chatArea.append("Vous : " + messageText + "\n");
}

clientInvite.send(json.toString());
inputField.setText("");
```

**Format JSON du message privé**
```json
{
  "type": "message",
  "subtype": "private",
  "to": "pseudo_destinataire",
  "content": "Message confidentiel"
}
```

#### Affichage différencié des messages

**Méthode `afficheMessage()` dans `ChatInterfacePerso`**
```java
try {
    JSONObject json = new JSONObject(lastMessageJSON);
    
    String type = json.optString("type");
    if (!"message".equals(type)) {
        return;
    }
    
    String subtype = json.optString("subtype");
    String from = json.optString("from");
    String content = json.optString("content");
    
    if ("private".equals(subtype)) {
        // Affichage de message "privé" avec préfixe
        chatArea.append("**[MP de " + from + "]** " + content + "\n");
    } else {
        // Affichage de message "global"
        chatArea.append(from + " : " + content + "\n");
    }
    
    // Placer le curseur à la fin du contenu
    chatArea.setCaretPosition(chatArea.getDocument().getLength());
    
} catch (Exception e) {
    System.out.println("Erreur dans afficheMessage() " + e.getMessage());
}
```

#### Mise à jour du modèle `Message`

**Ajout de l'attribut `subtype`**
```java
public Message(String type, String subtype, String from, String to, String content, String timestamp) {
    this.type = type;
    this.subtype = subtype;
    this.from = from;
    this.to = to;
    this.content = content;
    this.timestamp = timestamp;
}
```

#### Bouton Global pour désélectionner

**Implémentation**
```java
globalButton.addActionListener(e -> userList.clearSelection());

userList.addListSelectionListener(e -> {
    String selected = userList.getSelectedValue();
    if (selected != null) {
        globalButton.setText("↩ Global");
    } else {
        globalButton.setText("Global");
    }
});
```

#### Fonctionnalités bonus - Couleurs personnalisées

**Méthode `getColorForUser(String user)` dans `ChatInterfacePerso`**
```java
public Color getColorForUser(String user) {
    if (userColors.containsKey(user)) {
        return userColors.get(user);
    }
    
    int hash = Math.abs(user.hashCode());
    int r = (hash >> 16) & 0xFF;
    int g = (hash >> 8) & 0xFF;
    int b = hash & 0xFF;
    
    // Empêcher d'avoir du noir ou du blanc
    r = (r + 100) % 256;
    g = (g + 100) % 256;
    b = (b + 100) % 256;
    
    Color color = new Color(r, g, b);
    userColors.put(user, color);
    return color;
}
```

**Méthode `appendFormattedMessage(String from, String content, boolean isPrivate)`**
```java
public void appendFormattedMessage(String from, String content, boolean isPrivate) {
    try {
        Style fullStyle = chatArea.addStyle("full_" + from, null);
        StyleConstants.setForeground(fullStyle, getColorForUser(from));
        StyleConstants.setBold(fullStyle, true);
        
        String textToInsert;
        if (isPrivate) {
            textToInsert = "**[MP de " + from + "]** " + content + "\n";
        } else {
            textToInsert = from + " : " + content + "\n";
        }
        
        doc.insertString(doc.getLength(), textToInsert, fullStyle);
        chatArea.setCaretPosition(doc.getLength());
        
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

### Résultat de la journée
-  Liste des utilisateurs connectés affichée dynamiquement  
-  Possibilité de choisir entre message global et message privé  
-  Traitement correct des messages selon le subtype  
-  Messages privés invisibles pour les autres utilisateurs  
-  Affichage clair des messages selon leur nature  
-  Couleurs personnalisées pour chaque utilisateur

---

## 📖 Jour 5 - Gestion des statuts et finalisation du projet

### Objectifs de la journée
Gérer les statuts des utilisateurs, améliorer l'interface graphique et finaliser le projet.

### Fonctionnalités développées

#### Gestion des statuts utilisateur

**Dans la classe `ChatInterfacePerso`**

**Déclaration des propriétés**
```java
private final Map<String, String> userStatuses = new HashMap<>();
private final JComboBox<String> statusComboBox = new JComboBox<>(new String[] {
    "En ligne", "Absent", "Invisible", "Indisponible"
});
```

**Ajout du panneau de statut**
```java
// Panel en haut pour le statut
JPanel topPanel = new JPanel(new BorderLayout());
topPanel.add(new JLabel("Statut : "), BorderLayout.WEST);
topPanel.add(statusComboBox, BorderLayout.CENTER);

// Listener pour envoyer le statut lorsqu'il est changé
statusComboBox.addActionListener(e -> envoyerStatut());

// Ajouter ce panel en haut de la fenêtre
contentPane.add(topPanel, BorderLayout.NORTH);
```

#### Envoi du statut au serveur

**Méthode `envoyerStatut()`**
```java
JSONObject json = new JSONObject();
json.put("type", "status");
json.put("user", clientInvite.getPseudo());
json.put("state", state);
```

**Format JSON**
```json
{
  "type": "status",
  "user": "alice",
  "state": "away"
}
```

#### Affichage des utilisateurs avec leur statut

**Classe interne `UserStatusRenderer`**

Cette classe permet d'afficher chaque utilisateur avec un point coloré selon son statut.

**Méthode `getListCellRendererComponent()`**
```java
@Override
public Component getListCellRendererComponent(
    JList<?> list, Object value, int index, boolean isSelected, boolean cellHasFocus
) {
    String username = (String) value;
    String status = userStatuses.getOrDefault(username, "online");
    
    Color dotColor = switch (status) {
        case "online" -> Color.GREEN;
        case "away" -> Color.ORANGE;
        case "dnd" -> Color.RED;
        case "invisible" -> Color.GRAY;
        default -> Color.GREEN;
    };
    
    // Création du label avec icône
    JLabel label = new JLabel(username);
    label.setIcon(createStatusDot(dotColor));
    
    // Gestion de la sélection
    if (isSelected) {
        label.setBackground(list.getSelectionBackground());
        label.setForeground(list.getSelectionForeground());
        label.setOpaque(true);
    }
    
    return label;
}
```

**Méthode `createStatusDot(Color color)`**
```java
private Icon createStatusDot(Color color) {
    return new Icon() {
        @Override
        public void paintIcon(Component c, Graphics g, int x, int y) {
            g.setColor(color);
            g.fillOval(x, y, 10, 10);
        }
        
        @Override
        public int getIconWidth() { return 10; }
        
        @Override
        public int getIconHeight() { return 10; }
    };
}
```

#### Correction du bug de réinitialisation du statut

**Problème identifié**
Le statut de l'utilisateur local était réinitialisé à "online" après chaque événement dans le chat.

**Solution : modification de la méthode `updateUserList()`**
```java
if (localUser != null && !userMap.containsKey(localUser)) {
    
    // Éviter qu'il me remette à chaque événement en couleur verte en écrasant mon statut existant
    if (!userStatuses.containsKey(localUser)) {
        
        String currentStatus = (String) statusComboBox.getSelectedItem();
        String normalizedStatus = switch (currentStatus) {
            case "Absent" -> "away";
            case "Indisponible" -> "dnd";
            case "Invisible" -> "invisible";
            case "En ligne", "Disponible" -> "online";
            default -> "online";
        };
        userStatuses.put(localUser, normalizedStatus);
        
        System.out.println("Statut local déterminé depuis statusComboBox : " + normalizedStatus);
    } else {
        System.out.println("Statut local conservé : " + userStatuses.get(localUser));
    }
    
    if (!userListModel.contains(localUser)) {
        userListModel.addElement(localUser);
    }
    
    System.out.println("Ajout manuel de l'utilisateur local : " + localUser);
}
```

#### Finalisation graphique

-  Vérification de l'alignement et de la lisibilité de tous les composants
-  Ajout de bordures, marges et couleurs pour améliorer l'expérience utilisateur
-  Gestion de la redimension des composants
-  Amélioration de la réactivité de l'application

#### Tests et débogage

-  Tests de connexion/déconnexion
-  Tests d'envoi de messages globaux et privés
-  Tests de changement de statut
-  Vérification de la mise à jour de la liste des utilisateurs

### Résultat de la journée
- Application complète, testée et stable  
-  Interface Swing finale avec gestion des statuts  
- Documentation complète (README + captures)  
-  Projet Maven archivable  
- Correction du bug de réinitialisation du statut

---

## 📖 Jour EXTRA - Restructuration du projet

### Objectifs de la journée
Réorganiser l'architecture du projet selon le modèle MVC pour une meilleure maintenabilité.

### Nouvelle structure du projet

```
classcord-client/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── fr/
│   │   │   │   ├── classcord/
│   │   │   │   │   ├── controller/
│   │   │   │   │   │   ├── AuthController.java
│   │   │   │   │   │   ├── ChatController.java
│   │   │   │   │   │   ├── LoginController.java
│   │   │   │   │   │   ├── SessionController.java
│   │   │   │   │   ├── model/
│   │   │   │   │   │   ├── ClientInvite.java
│   │   │   │   │   │   ├── CurrentUser.java (inactif)
│   │   │   │   │   │   ├── User.java (inactif)
│   │   │   │   │   │   ├── Message.java (inactif)
│   │   │   │   │   │   ├── UserColorManager.java
│   │   │   │   │   ├── ui/
│   │   │   │   │   │   ├── ChatPersoUI.java
│   │   │   │   │   │   ├── ChatUI.java (inactif)
│   │   │   │   │   │   ├── ChoixModeUI.java
│   │   │   │   │   │   ├── ConnectToServeurUI.java
│   │   │   │   │   │   ├── GuestUI.java
│   │   │   │   │   │   ├── LoginUI.java
│   │   │   │   │   │   ├── UserStatusRenderer.java
│   │   │   │   │   ├── app/
│   │   │   │   │   │   ├── App.java (inactif)
├── pom.xml
```

### Architecture MVC mise en place

**Model (Modèle)**
- `ClientInvite` : Gestion de la connexion et des échanges réseau
- `Message` : Représentation des messages
- `User` : Représentation des utilisateurs
- `UserColorManager` : Gestion des couleurs par utilisateur

**View (Vue)**
- `ConnectToServeurUI` : Interface de connexion au serveur
- `ChoixModeUI` : Choix du mode (invité/utilisateur)
- `LoginUI` : Interface d'authentification
- `GuestUI` : Interface pour mode invité
- `ChatPersoUI` : Interface principale de chat
- `UserStatusRenderer` : Rendu des utilisateurs avec statuts

**Controller (Contrôleur)**
- `AuthController` : Logique d'authentification
- `ChatController` : Logique de gestion du chat
- `LoginController` : Logique de connexion
- `SessionController` : Gestion de la session utilisateur

### Tests de vérification

- Validation de l’ensemble des fonctionnalités principales
- Tests multiclients réalisés en septembre (fonctionnalités partiellement opérationnelles)

### Résultat de la restructuration
- Architecture MVC respectée  
- Séparation claire des responsabilités  
- Code plus maintenable et évolutif  
- Préparation pour les tests multiclient

---

## 📚 Sources et Documentation

### Ressources utilisées
- Aide de l'IA pour certaines implémentations
- [Stack Overflow - Animated GIF in JFrame](https://stackoverflow.com/questions/34719923/how-do-i-load-an-animated-gif-within-my-jframe-while-a-long-process-is-running)
- [Stack Overflow - setForeground in Java Applet](https://stackoverflow.com/questions/34262447/java-applet-setforeground-what-exactly-it-does-and-how-to-see-its-effect)
- Projet du Jeu en Java (Cours SLAM)

### Captures d'écran disponibles

Les captures d'écran suivantes sont disponibles dans le dossier `images/` :
- `ConnectToServeur` : Interface de connexion au serveur
- `ChoixModeUI` : Interface de choix du mode
- `LoginUI` : Interface d'authentification
- `GuestUI` : Interface mode invité
- `ChatInterface` : Interface de chat basique (Jour 2)
- `ChatInterfacePerso` : Interface de chat personnalisée
- `spinner-loading` : Icône de chargement animée
- `Interface Swing final` : Interface finale du projet

---

## 🎯 Conclusion

Ce projet a permis de développer une application de messagerie complète en Java avec :
- Communication réseau via sockets TCP
- Gestion d'authentification et de session
- Interface graphique Swing riche et responsive
- Architecture MVC bien structurée
- Fonctionnalités avancées (messages privés, statuts, couleurs personnalisées)

Le projet est opérationnel et prêt pour des tests multiclient approfondis.
