# Lezione 4 — Operazioni di base con i Socket per realizzare applicazioni con funzionalità di rete

---

## Realizzazione di applicazioni Client/Server

Le applicazioni **client/server** dividono la logica applicativa in due programmi separati:  
- un *client*, eseguito dall'utente finale,  
- un *server*, che gestisce le richieste.  

La comunicazione avviene con meccanismo **richiesta/risposta**:  
il client invia una richiesta (es. login, caricamento dati, richiesta pagina), il server la elabora e risponde con un risultato.

---

## Implementazione Server

Un server deve poter gestire **più utenti contemporaneamente**, quindi utilizza **programmazione concorrente**.  
Ogni richiesta viene gestita da un **nuovo thread**.

### Struttura generale del Server

- **Thread principale**: avviato all’esecuzione, resta in ascolto di nuove connessioni.  
- **Thread figli**: ogni volta che arriva una nuova connessione, il main thread la accetta e genera un thread dedicato che gestisce la comunicazione.  
- Ogni thread figlio chiude la connessione al termine.

### Esempio di codice (Server Java)

```java
public class PrimoServer extends Thread {

    private ServerSocket serverSocket;
    private boolean running = false;

    public void startServer() {
        try {
            serverSocket = new ServerSocket(5555);
            this.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void stopServer() {
        running = false;
        this.interrupt();
    }

    @Override
    public void run() {
        running = true;
        while (running) {
            try {
                Socket socket = serverSocket.accept();
                RequestHandler requestHandler = new RequestHandler(socket);
                requestHandler.start();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        PrimoServer server = new PrimoServer();
        server.startServer();
    }
}
```

---

## Implementazione Client

Il client ha una struttura più semplice:
1. Creare un socket e connettersi al server.  
2. Inviare una richiesta.  
3. Ricevere una risposta.  
4. Chiudere la connessione.

### Esempio di codice (Client Java)

```java
try {
    Socket socket = new Socket("127.0.0.1", 5555);

    PrintStream out = new PrintStream(socket.getOutputStream());
    BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));

    out.println("RICHIESTA");
    String line = in.readLine();

    System.out.println("Risposta del server: " + line);

    in.close();
    out.close();
    socket.close();
} catch (Exception e) {
    e.printStackTrace();
}
```

> Se il server non è raggiungibile, viene lanciata un’eccezione da gestire nel blocco `catch`.

---

## Esercizio 1

Modificare l’applicazione per **verificare le credenziali sul server** partendo dall'applicazione JavaFX realizzata creando un nuovo server (progetto addizionale - no GUI):  
- il client invia username e password,  
- il server li controlla e risponde “login valido” o “non valido”,  
- il client apre la seconda finestra solo in caso positivo.

---

## Operazioni bloccanti e interfaccia grafica (JavaFX)

Le operazioni di rete (socket) sono **bloccanti** e non vanno eseguite direttamente in un metodo `handle`, altrimenti l’interfaccia si blocca.

Esempio di test:

```java
Thread.sleep(5000);
```
Durante l’attesa, la GUI risulta congelata.

---

## Soluzione: usare un Task JavaFX (thread separato)

Per evitare il blocco dell’interfaccia, le operazioni di rete si eseguono in un **thread separato** tramite la classe `Task` di JavaFX.

### Esempio completo

```java
Task<Void> task = new Task<Void>() {
    @Override
    public Void call() {
        try {
            Socket socket = new Socket("127.0.0.1", 5555);

            PrintStream out = new PrintStream(socket.getOutputStream());
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));

            out.println("LOGIN utente");
            String line = in.readLine();

            // Aggiornamento interfaccia grafica in modo sicuro
            Platform.runLater(() -> {
                campoUtente.setDisable(false);
                primaryButton.setDisable(false);
                primaryButton.setText("Riprova");
            });

            in.close();
            out.close();
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
};
new Thread(task).start();
```

### Spiegazione

- Il `Task` viene eseguito su un thread separato e non blocca la GUI.  
- L’interfaccia può essere aggiornata solo tramite `Platform.runLater()`, che esegue codice nel thread JavaFX principale.  
- È buona pratica disabilitare i campi durante la connessione e riabilitarli dopo la risposta del server.

---

## Esercizio 2

Inserire l’invio e la ricezione delle credenziali in un `Task`, disabilitando i campi e il pulsante durante l’attesa della risposta, e riabilitandoli con `Platform.runLater()`.

---

> Fonte: appunti della **Lezione 4 — Operazioni di base con i Socket per applicazioni con funzionalità di rete**, corso *Programmazione avanzata*.
