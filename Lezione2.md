# Programmazione avanzata — Lezione 2

---

## Indice
- [Obiettivi](#obiettivi)
- [Argomenti](#argomenti)
- [Guida operativa](#guida-operativa)
- [Esempi pratici](#esempi-pratici)
- [Comandi utili](#comandi-utili)
- [Esercizi](#esercizi)
- [Checkpoint](#checkpoint)
- [Troubleshooting](#troubleshooting)
- [Note](#note)

---

## Obiettivi
- Consolidare l'ambiente creato in **Lezione 1**.
- Integrare il progetto con **JavaFX (FXML + Controller)**, **JDBC/MySQL** e dipendenze Maven.
- Eseguire e debuggare una demo completa.

## Argomenti
- JavaFX: struttura App/FXML/Controller.
- JDBC & MySQL: connessione, query di prova.
- Maven: dipendenze e build.

---

## Guida operativa
> [!TIP]
> Verifica prima: `java -version` deve essere 21 e `mvn -v` funzionante.

### 1) Configurazione progetto (Maven)
- Apri il progetto in NetBeans.
- Aggiorna `pom.xml` con i moduli necessari (JavaFX, MySQL).
- Esegui **Build** per scaricare le librerie.

### 2) JavaFX (se previsto)
- Aggiungi dipendenze `javafx-controls` e `javafx-fxml`.
- Collega `main.fxml` a un `Controller` con annotazioni `@FXML`.
- Esegui l'app e verifica l'handler del bottone.

### 3) JDBC/MySQL (se previsto)
- Aggiungi `mysql-connector-j`.
- Testa una connessione `SELECT 1` su un DB locale.

---

## Esempi pratici

### `pom.xml` (estratto)
```xml
<dependencies>
  <!-- JavaFX -->
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-controls</artifactId>
    <version>22.0.1</version>
  </dependency>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-fxml</artifactId>
    <version>22.0.1</version>
  </dependency>

  <!-- MySQL Connector/J -->
  <dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.4.0</version>
  </dependency>
</dependencies>
```

### JavaFX minimal
```java
// App.java
import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class App extends Application {
  @Override
  public void start(Stage stage) throws Exception {
    FXMLLoader fxml = new FXMLLoader(App.class.getResource("main.fxml"));
    stage.setScene(new Scene(fxml.load(), 600, 400));
    stage.setTitle("Lezione 2 Demo");
    stage.show();
  }
  public static void main(String[] args) { launch(args); }
}
```

```xml
<!-- main.fxml -->
<?xml version="1.0" encoding="UTF-8"?>
<?import javafx.scene.layout.VBox?>
<?import javafx.scene.control.*?>
<VBox spacing="12.0" xmlns="http://javafx.com/javafx" xmlns:fx="http://javafx.com/fxml"
      fx:controller="it.unipi.demo.MainController">
  <Label text="Lezione 2 — Demo JavaFX"/>
  <Button text="Click me" onAction="#onClick"/>
</VBox>
```

```java
// MainController.java
import javafx.fxml.FXML;
import javafx.scene.control.Alert;

public class MainController {
  @FXML
  public void onClick() {
    new Alert(Alert.AlertType.INFORMATION, "Hello from Lezione 2!").showAndWait();
  }
}
```

### JDBC ping (MySQL)
```java
import java.sql.*;

public class DbPing {
  public static void main(String[] args) throws Exception {
    String url = "jdbc:mysql://localhost:3306/test";
    try (Connection c = DriverManager.getConnection(url, "root", "PASSWORD")) {
      try (PreparedStatement ps = c.prepareStatement("SELECT 1");
           ResultSet rs = ps.executeQuery()) {
        if (rs.next()) System.out.println("DB OK: " + rs.getInt(1));
      }
    }
  }
}
```

---

## Comandi utili
```bash
mvn -q clean package
mvn -q exec:java
mvn -q dependency:tree
```

---


## Troubleshooting
- **JavaFX JPMS**: aggiungi i moduli richiesti e configura VM options se usi moduli.
- **MySQL**: verifica credenziali/porta e il connettore nel classpath.
- **Dipendenze**: `mvn -U clean package` per aggiornare la cache.

