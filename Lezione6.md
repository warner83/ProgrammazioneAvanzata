# Lezione 6 — Interazione con il database MySQL 

---

## 1) Dipendenze (MySQL Connector/J)

Per connettersi a un database **MySQL** da Java è necessario aggiungere la libreria **mysql-connector-java** tra le dipendenze Maven del progetto.

```xml
<!-- pom.xml -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.33</version>
</dependency>
```

> Se il progetto è modulare, ricordarsi delle relative `requires` in `module-info.java` (se presenti).

---

## 2) Creazione del database e inserimento dei primi dati (MySQL Workbench)

1. **Accedi** a MySQL Workbench con le credenziali di `root`.
2. **Crea** un nuovo database (schema) e una **tabella** per gli utenti.
3. **Inserisci** almeno un utente di prova.

Equivalente SQL (comandi eseguibili dalla scheda `Query` di Workbench):

```sql
CREATE DATABASE IF NOT EXISTS prima_app;
USE prima_app;

CREATE TABLE IF NOT EXISTS utenti (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(50)  NOT NULL,
  password VARCHAR(255) NOT NULL
);

INSERT INTO utenti (nome, password) VALUES ('utente1', 'password');
```

---

## 3) Connessione JDBC di base

Esempio di apertura connessione usando **DriverManager** e try-with-resources:

```java
try (Connection co = DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/prima_app", "root", "root")) {
    // Connessione OK
} catch (SQLException e) {
    logger.error(e.getMessage());
}
```

---

## 4) Esecuzione query con `Statement`

Per leggere i dati con una query **non parametrica** si può usare `Statement`:

```java
try (Connection co = DriverManager.getConnection(
         "jdbc:mysql://localhost:3306/prima_app", "root", "root");
     Statement st = co.createStatement();
     ResultSet rs = st.executeQuery("SELECT id, nome, password FROM utenti")) {

    while (rs.next()) {
        int id = rs.getInt("id");
        String nome = rs.getString("nome");
        String pass = rs.getString("password");
        System.out.printf("Utente #%d -> %s / %s%n", id, nome, pass);
    }
} catch (SQLException e) {
    logger.error(e.getMessage());
}
```

> `ResultSet` scorre riga per riga; usa i metodi `getXxx` con il **nome colonna** o l’**indice** (1‑based).

---

## 5) Query parametriche con `PreparedStatement`

Per query con **condizioni** o per **riutilizzare** la stessa query con parametri diversi, si usa `PreparedStatement`.

### 5a) Esempio di **INSERT** parametrico (dallo schema della lezione)

```java
try (Connection co = DriverManager.getConnection(
         "jdbc:mysql://localhost:3306/prima_app", "root", "root");
     PreparedStatement ps = co.prepareStatement(
         "INSERT INTO utenti (nome, password) VALUES (?, ?)")) {

    ps.setString(1, "utente1");
    ps.setString(2, "password");

    ps.executeUpdate();
} catch (SQLException e) {
    logger.error(e.getMessage());
}
```

### 5b) Esempio di **SELECT** parametrica (verifica credenziali)

```java
public boolean verificaCredenziali(String user, String pwd) {
    String sql = "SELECT 1 FROM utenti WHERE nome = ? AND password = ?";
    try (Connection co = DriverManager.getConnection(
             "jdbc:mysql://localhost:3306/prima_app", "root", "root");
         PreparedStatement ps = co.prepareStatement(sql)) {

        ps.setString(1, user);
        ps.setString(2, pwd);

        try (ResultSet rs = ps.executeQuery()) {
            return rs.next(); // true se esiste almeno una riga
        }
    } catch (SQLException e) {
        logger.error(e.getMessage());
        return false;
    }
}
```

> `PreparedStatement` previene SQL injection e migliora le prestazioni nelle esecuzioni multiple della stessa query.

---

## 6) Integrazione lato Server (richiesta/risposta)

Nel server, la verifica può essere eseguita nel punto in cui si riceve la richiesta **login** dal client:

```java
// Esempio in un RequestHandler
String nome = ...;     // estrai dal messaggio del client
String password = ...; // estrai dal messaggio del client

boolean ok = verificaCredenziali(nome, password);
PrintStream out = new PrintStream(socket.getOutputStream());
out.println(ok ? "LOGIN_OK" : "LOGIN_KO");
```

---

## 7) Gestione eccezioni

Tutte le operazioni JDBC possono lanciare **SQLException**: gestire gli errori e mostrare messaggi chiari (ad es. con `logger.error(e.getMessage())`).

---

## 8) Esercizio

- **Modifica il server** in modo da **verificare le credenziali** sul database (tabella `utenti`).  
- Le credenziali possono essere **inserite a mano** con MySQL Workbench.  
- **Opzionale**: durante l’inizializzazione del server, creare lo **schema** e la **tabella** (se non esistono) e inserire **utenti di default**.

---

> Materiale tratto dalla **Lezione 6 — Interazione con il database MySQL** (ordine e passaggi fedeli, inclusi gli esempi di codice presenti nel documento).
