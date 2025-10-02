# Lezione 1 — Installazione ambiente e prima applicazione 

> Corso: **Programmazione avanzata**  
> Obiettivo: predisporre l’ambiente Java, creare la prima app con Maven, capire il ruolo di Maven e aggiungere una dipendenza.

---

## Requisiti e strumenti

- **OpenJDK 21** – JDK e JRE open-source.  
  Download: <https://docs.microsoft.com/en-us/java/openjdk/download>  
  Durante l’installazione seleziona **“Set JAVA_HOME variable”**.

- **Apache NetBeans** – IDE con supporto Maven, JavaFX, debugging.

- **JavaFX & Scene Builder** – Progettazione GUI (il collegamento a NetBeans si configura dopo l’installazione).

- **MySQL & MySQL Workbench** – DB server + tool grafico + driver JDBC.

---

## Installazione (sintesi operativa)

1. **OpenJDK 21**  
   Segui il wizard e *spunta* “Set JAVA_HOME variable”.

2. **NetBeans 14**  
   Installazione classica “Next → Next → Finish”.

3. **JavaFX Scene Builder**  
   Installa e **collega in NetBeans** (Tools → Options → Java → Path a Scene Builder).

4. **MySQL**  
   - Completa l’installer (server + Workbench + connettori).  
   - **Imposta la password di `root`**.  
   - Esegui il **test di connettività** proposto dall’installer.

---

## Prima applicazione con Maven in NetBeans

1. **New → Project** → categoria **Java with Maven** → *Java Application*.  
2. Scegli **GroupId**, **ArtifactId** e **Package** (es. `it.unipi.firstapp`).  
3. NetBeans genera uno **scheletro** con `main` già eseguibile.

### Debug rapido
- Metti un **breakpoint** cliccando sul margine sinistro (accanto ai numeri di riga).  
- Avvia in **Debug** per ispezionare variabili/stack.

---

## Cos’è Maven (e perché lo usiamo)

Maven è un **gestore di progetto**: compila, risolve **dipendenze**, esegue test, impacchetta.  
Tutto è descritto nel file **`pom.xml`** (generato automaticamente).

Esempio minimale di `pom.xml` di progetto (con Log4j incluso):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>it.unipi.firstapp</groupId>
  <artifactId>FirstApp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <dependencies>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.18.0</version>
    </dependency>
  </dependencies>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <exec.mainClass>it.unipi.firstapp.FirstApp</exec.mainClass>
  </properties>
</project>
```

---

## Aggiungere una dipendenza (via NetBeans)

1. Apri `pom.xml` → **tasto destro** → **Insert code** → **Add Dependency**.  
2. Cerca su <https://mvnrepository.com/> il **GroupId** / **ArtifactId** / **Version**.  
3. Maven scarica la libreria al primo *Build*.

### Esempio: HTTPClient (richiesta HTTP e stampa risposta)

```java
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.HttpEntity;
import org.apache.http.util.EntityUtils;

public class HttpDemo {
    public static void main(String[] args) throws Exception {
        CloseableHttpClient httpClient = HttpClients.createDefault();

        HttpGet request = new HttpGet("https://www.google.com");
        CloseableHttpResponse response = httpClient.execute(request);

        System.out.println(response.getProtocolVersion());
        System.out.println(response.getStatusLine().getStatusCode());
        System.out.println(response.getStatusLine().getReasonPhrase());
        System.out.println(response.getStatusLine().toString());

        HttpEntity entity = response.getEntity();
        if (entity != null) {
            String result = EntityUtils.toString(entity);
            System.out.println(result);
        }
    }
}
```

Suggerimento: Usa **Source → Fix Imports** in NetBeans per aggiungere automaticamente gli import.

---

## Note e link utili

- OpenJDK 21 download: <https://docs.microsoft.com/en-us/java/openjdk/download>  
- Repository Maven: <https://mvnrepository.com/>  
- In caso di problemi con import/dipendenze: **Clean & Build** dal menu di progetto.

---

> Fonte: appunti della **Lezione 1 — Installazione ambiente e prima applicazione**, corso *Programmazione avanzata*.
