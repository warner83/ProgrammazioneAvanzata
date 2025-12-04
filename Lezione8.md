# Lezione 8 — Importazione dati da servizi web e visualizzazione con TableView 
---

## Importazione dei dati da un servizio web

### Servizio web

Molto spesso le applicazioni recuperano informazioni da **servizi web**.  
Un servizio web è un tipo particolare di applicazione web che:

- non espone un’interfaccia utente grafica;
- espone invece un’interfaccia per altre applicazioni per lo scambio di dati.

I dati vengono tipicamente codificati in formato **JSON** o **XML**.

Esistono molti servizi disponibili; un elenco (non esaustivo) è consultabile su:

- https://free-apis.github.io/

Un’applicazione può importare i dati e interpretarli tramite le librerie **GSON** o **XSTREAM**.  
Nel caso di un flusso JSON è anche possibile leggerlo “al volo” senza creare preventivamente una struttura dati dedicata.

---

### Recupero dati via HTTP

Primo passo: leggere i dati dall’URL del servizio tramite una richiesta HTTP:

```java
URL url = new URL("http://131.114.51.64:9090/api/f1/drivers.json?limit=10&offset=20");
HttpURLConnection con = (HttpURLConnection) url.openConnection();
con.setRequestMethod("GET");

BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));

String inputLine;
StringBuffer content = new StringBuffer();
while ((inputLine = in.readLine()) != null) {
    content.append(inputLine);
}
in.close();
```

Spiegazione:

1. Si specifica l’URL del servizio web da cui vogliamo scaricare i dati.
2. Si apre la connessione con `HttpURLConnection` e si imposta il metodo `GET`.
3. Si leggono tutte le righe restituite dal servizio e si accumulano in un’unica variabile `content`.

Nell’esempio, i dati in formato JSON sono recuperati dall’URL:

- `http://131.114.51.64:9090/api/f1/drivers.json`

(che è una copia locale di `http://ergast.com/api/f1/drivers.json`, raggiungibile solo da rete UNIPI).

Per rendere i dati più gestibili, l’URL supporta dei **query parameters**:

- `limit` – numero di piloti restituiti;
- `offset` – indice di partenza del blocco di dati richiesto.

---

## Esercizio: importare i dati nel database

Importare i dati dei piloti (anche solo una parte) in un database dopo averli recuperati dal servizio web.

Suggerimento: il database deve essere creato con una struttura compatibile allo schema dei dati dei drivers (ID, nome, cognome, nazionalità, data di nascita).

Per usare `mysql-connector-java` in un’app JavaFX è necessario aggiungere nel `module-info.java`:

```java
requires java.sql;
requires java.base;
requires java.desktop;
```

---

## Visualizzazione dei dati in una tabella JavaFX

I dati inseriti nel database vengono mostrati all’utente tramite una **TableView** di JavaFX.

### Classe `Driver` (JavaBean)

Per prima cosa si definisce una classe che rappresenta il dato da mostrare:

```java
public class Driver {

    private final int id;
    private final String name;
    private final String lastname;
    private final String nationality;
    private final String birthdate;

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getLastname() {
        return lastname;
    }

    public String getNationality() {
        return nationality;
    }

    public String getBirthdate() {
        return birthdate;
    }

    public Driver(int id, String name, String lastname, String nationality, String birthdate) {
        this.id = id;
        this.name = name;
        this.lastname = lastname;
        this.nationality = nationality;
        this.birthdate = birthdate;
    }
}
```

### TableView in Scene Builder

Nell’interfaccia si crea un elemento `TableView<Driver>` con JavaFX Scene Builder.  
È importante:

- eliminare le colonne create automaticamente (C1, C2, …) perché verranno definite nel codice;
- impostare il campo `fx:id` della TableView (ad esempio `driversTable`).

### Collegamento nel controller

Nel controller della finestra:

```java
@FXML
TableView<Driver> driversTable = new TableView<>();

private ObservableList<Driver> ol;
```

Nella funzione `initialize()` si creano le colonne e si collegano ai campi della classe `Driver`:

```java
@FXML
public void initialize() {
    TableColumn idCol = new TableColumn("ID");
    idCol.setCellValueFactory(new PropertyValueFactory<>("id"));

    TableColumn nomeCol = new TableColumn("Name");
    nomeCol.setCellValueFactory(new PropertyValueFactory<>("name"));

    TableColumn lastCol = new TableColumn("LastName");
    lastCol.setCellValueFactory(new PropertyValueFactory<>("lastname"));

    TableColumn nationalityCol = new TableColumn("Nationality");
    nationalityCol.setCellValueFactory(new PropertyValueFactory<>("nationality"));

    TableColumn dateCol = new TableColumn("BirthDate");
    dateCol.setCellValueFactory(new PropertyValueFactory<>("birthdate"));

    driversTable.getColumns().addAll(idCol, nomeCol, lastCol, nationalityCol, dateCol);

    ol = FXCollections.observableArrayList();

    driversTable.setItems(ol);
}
```

- Le colonne (`TableColumn`) sono collegate ai campi della classe `Driver` tramite `PropertyValueFactory`.
- `driversTable.setItems(ol)` collega la tabella a una `ObservableList` tale che ogni modifica alla lista si rifletta automaticamente sulla tabella.

### Caricamento dei dati dal database

Per leggere i dati dal database e popolare la tabella:

```java
try (Connection co = DriverManager.getConnection(
         "jdbc:mysql://localhost:3306/drivers", "root", "root");
     Statement st = co.createStatement()) {

    ResultSet rs = st.executeQuery("SELECT * FROM drivers");
    while (rs.next()) {
        Driver d = new Driver(
            rs.getInt("ID"),
            rs.getString("Name"),
            rs.getString("LastName"),
            rs.getString("Nationality"),
            rs.getString("BirthDate")
        );
        ol.add(d);
    }
} catch (SQLException e) {
    logger.error(e.getMessage());
}
```

Esercizio: aggiungere la tabella al programma e caricare, al momento dell’inizializzazione, i dati dal database.

---

## Creazione di menu a scomparsa (ContextMenu)

La tabella può essere configurata per includere un **menu a scomparsa** (ContextMenu) associato agli elementi mostrati.  
Esempio: menu per cancellare il driver attualmente selezionato.

Si aggiunge un `ContextMenu` con almeno un `MenuItem` alla TableView e si collega una funzione del controller alla voce di menu.

### Metodo `remove()` nel controller

```java
@FXML
public void remove() {

    try (Connection co = DriverManager.getConnection(
             "jdbc:mysql://localhost:3306/drivers", "root", "root");
         PreparedStatement ps = co.prepareStatement(
             "DELETE FROM drivers WHERE ID = ?")) {

        ps.setInt(1, driversTable.getSelectionModel()
                                 .getSelectedItem()
                                 .getId());
        ps.executeUpdate();

        ol.remove(driversTable.getSelectionModel().getSelectedItem());

    } catch (SQLException e) {
        logger.error(e.getMessage());
    }
}
```

- Si apre una connessione al database.
- Si esegue un `DELETE` sul driver selezionato (ID preso dalla TableView).
- Si rimuove l’istanza corrispondente anche dalla lista `ol`, così la tabella si aggiorna.

Esercizio: aggiungere il menu a scomparsa per la cancellazione dei drivers.

---

## Gestione delle operazioni con `Task`

Molte operazioni sul database possono richiedere tempo.  
Per evitare di bloccare l’interfaccia JavaFX, le operazioni vanno eseguite su un thread separato, ad esempio usando `Task`.

### Esempio: cancellazione con `Task`

```java
Task<Void> task = new Task<Void>() {
    @Override
    public Void call() {
        try (Connection co = DriverManager.getConnection(
                 "jdbc:mysql://localhost:3306/drivers", "root", "root");
             PreparedStatement ps = co.prepareStatement(
                 "DELETE FROM drivers WHERE ID = ?")) {

            ps.setInt(1, driversTable.getSelectionModel()
                                     .getSelectedItem()
                                     .getId());

            ps.executeUpdate();

            ol.remove(driversTable.getSelectionModel().getSelectedItem());

        } catch (SQLException e) {
            logger.error(e.getMessage());
        }

        return null;
    }
};

new Thread(task).start();
```

In questo modo:

- la logica di cancellazione dal database viene eseguita su un thread secondario;
- l’interfaccia JavaFX rimane reattiva mentre l’operazione è in corso.

---

## Esercizio riassuntivo

1. Recuperare i dati dei piloti F1 dal servizio web (`drivers.json`) usando `HttpURLConnection`.  
2. Importare (una parte di) questi dati in un database MySQL.  
3. Mostrare i dati in una `TableView<Driver>` configurata come visto sopra.  
4. Aggiungere un **ContextMenu** per:
   - cancellare il driver selezionato con `remove()`;
   - eseguire la cancellazione all’interno di un `Task` per non bloccare la GUI.

---
