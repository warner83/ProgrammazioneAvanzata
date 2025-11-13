# Lezione 3 — Operazioni di base con JavaFX per applicazioni con grafica 

---

## Configurazione

Prima di cominciare a sviluppare un’applicazione con interfaccia grafica è utile collegare **JavaFX Scene Builder** a NetBeans.  
In **Tools → Options → Java** inserisci il **percorso** del tool Scene Builder (campo *JavaFX*).

---

## Creazione progetto con JavaFX

Per creare un nuovo progetto seleziona: **Java with Maven → FXML JavaFX Maven Archetype**.  
Poi imposta **nome progetto**, **GroupId**, **ArtifactId**, **Package** e conferma.

NetBeans genera uno **scheletro** con:

- `App.java`: classe principale che avvia l’applicazione.
- `PrimaryController.java`: controller della **prima** interfaccia.
- `SecondaryController.java`: controller della **seconda** interfaccia.
- `primary.fxml` e `secondary.fxml`: layout (FXML) delle due scene.

Aprendo i file FXML si avvia automaticamente **Scene Builder**, dove puoi **modificare l’interfaccia** e le sue componenti.

---

## Scene Builder: selezione elementi e proprietà utili

Seleziona un elemento dall’albero a sinistra per modificarne le proprietà. Le proprietà tipiche da impostare sono:

- **fx:id** – variabile del controller collegata al componente (necessaria per accedervi via codice).  
- **Text** – testo mostrato dal componente (es. `Label`, `Button`).  
- **On Action** – nome del **metodo** nel controller invocato all’evento (es. click di un bottone).

> **Nota (macOS)**: su alcune macchine Apple può essere necessario aggiornare la versione di JavaFX nel `pom.xml` (ad es. JavaFX **23**).

In quel caso dobbiamo modificare le dipendenze nel `pom.xml` (Maven) come segue:
```xml
<dependencies>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-controls</artifactId>
    <version>23</version>
  </dependency>
  <dependency>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-fxml</artifactId>
    <version>23</version>
  </dependency>
</dependencies>
```

---

## Prima interfaccia: campi di login e pulsante

Nella **prima finestra** (primary.fxml) inserisci due campi (`TextField` e `PasswordField`) e un `Button`.  
Imposta gli **fx:id** (es. `usr`, `pwd`) e l’**On Action** del pulsante (es. `#login`).
Nel controller `PrimaryController.java` dichiara i riferimenti e implementa la logica:

```java
public class PrimaryController {

    @FXML private TextField usr;
    @FXML private PasswordField pwd;
    @FXML private Button primaryButton;

    @FXML
    private void login() throws IOException {
        String u = usr.getText();
        String p = pwd.getText();

        // Esempio di validazione semplice
        boolean ok = u != null && !u.isBlank() && p != null && !p.isBlank();

        if (ok) {
            // Carica la seconda interfaccia
            Parent root = FXMLLoader.load(getClass().getResource("secondary.fxml"));
            Scene scene = new Scene(root);
            Stage s = (Stage) primaryButton.getScene().getWindow();
            s.setScene(scene);
            s.show();
        } else {
            // Feedback all’utente
            primaryButton.setText("Riprova");
        }
    }
}
```

**Nota:** il cambio scena avviene recuperando lo `Stage` dalla scena corrente del pulsante e caricando `secondary.fxml` con `FXMLLoader`.

---

## Seconda interfaccia: immagine e pulsante

Apri **secondary.fxml** e aggiungi un’`ImageView` e un `Button`.  
Assegna **fx:id** all’`ImageView` (es. `mainImage`) e un handler al `Button` (es. `#toggleImage`).

Nel controller `SecondaryController.java`:

```java
public class SecondaryController {

    @FXML private ImageView mainImage;
    @FXML private Button primaryButton;

    @FXML
    public void initialize() {
        // Carica un’immagine dalle resources (vedi nota più sotto)
        Image img = new Image(getClass().getResource("/it/unipi/primaappinterfaccia/img/dog.png").toExternalForm());
        mainImage.setImage(img);
    }

    @FXML
    private void toggleImage() {
        if (mainImage != null) {
            mainImage.setVisible(!mainImage.isVisible());
        }
    }
}
```

---

## Esercizio (prima parte)

Modifica l’applicazione in modo che la **prima finestra** svolga il ruolo di **login**.  
Nella seconda schermata aggiungi un immagine e un pulsante. Premendo il secondo pulsante l'immagine mostrata deve essere sostituita con un'altra.

---

## Nota: struttura delle immagini per `getResource`

Per rendere semplici i riferimenti a immagini tramite `getResource`, inserisci i file qui:
```
src/main/resources/PACKAGE/img
```
dove `PACKAGE` è il package principale del progetto.  
Esempio se il package è `it.unipi.primaappinterfaccia`:
```
src/main/resources/it/unipi/primaappinterfaccia/img
```

---

## Creazione dinamica degli elementi

Gli elementi dell’interfaccia possono essere aggiunti anche **dinamicamente** via codice (non solo da FXML).  
**Scenario:** nella seconda interfaccia, alla pressione di un pulsante esistente, crea **dinamicamente un nuovo pulsante**.  
Il nuovo pulsante, se premuto, rende **invisibile** l’immagine.

**Passaggi:**
1. Creazione di un nuovo `Button` e configurazione proprietà.  
2. Definizione di un `EventHandler` con la funzione da invocare al click.  
3. Collegamento handler ↔ pulsante.  
4. Aggiunta del pulsante al contenitore della finestra.

### Riferimento al contenitore principale
Per eseguire il punto (4) serve un riferimento al contenitore (non creato di default).  
In `secondary.fxml`, assegna un **fx:id** al `VBox` principale (es. `stackSec`), poi dichiara la variabile nel controller.
Questa operazione puo' essere fatta dallo scene builder

**Estratto FXML**
```xml
<VBox fx:id="stackSec" xmlns="http://javafx.com/javafx/8"
      xmlns:fx="http://javafx.com/fxml/1"
      fx:controller="it.unipi.primaappinterfaccia.SecondaryController">
    <!-- nodi dell’interfaccia -->
</VBox>
```

**Controller**
```java
public class SecondaryController {

    @FXML private VBox stackSec;
    @FXML private ImageView mainImage;

    @FXML
    private void onCreateDynamicButton() {
        Button toggleBtn = new Button("Mostra/Nascondi immagine");

        EventHandler<ActionEvent> handler = evt -> {
            if (mainImage != null) mainImage.setVisible(!mainImage.isVisible());
        };

        toggleBtn.setOnAction(handler);
        stackSec.getChildren().add(toggleBtn);
    }
}
```

---

## Esercizio (seconda parte)

Creare **dinamicamente** un **secondo pulsante** quando viene premuto il primo.  
Il secondo pulsante deve modificare la **visibilità** dell’immagine (toggle) e aggiornare il **testo** del pulsante in base allo stato (es. “Nascondi immagine” / “Mostra immagine”).

---

*Questa ricostruzione segue l’ordine e i passaggi elencati in “Lezione 3.docx”; per i blocchi di codice mostrati come immagini nel documento originale, ho riprodotto il contenuto in forma testuale equivalente (FXML/Java/`pom.xml`).*
