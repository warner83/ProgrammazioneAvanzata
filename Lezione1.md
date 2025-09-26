# Programmazione avanzata — Lezione 1
**Installazione ambiente e prima applicazione**  


---

## Indice
- [Obiettivi](#obiettivi)
- [Prerequisiti](#prerequisiti)
- [Installazione rapida](#installazione-rapida)
- [Primo progetto Maven in NetBeans](#primo-progetto-maven-in-netbeans)
- [Maven in breve](#maven-in-breve)
- [Aggiungere una dipendenza](#aggiungere-una-dipendenza)
- [Esempi pratici](#esempi-pratici)
  - [Logging con Log4j](#logging-con-log4j)
  - [HTTP Client (Apache HttpClient 5)](#http-client-apache-httpclient-5)
- [Comandi utili di Maven](#comandi-utili-di-maven)
- [Esercizi](#esercizi)
- [Checkpoint](#checkpoint)
- [Troubleshooting](#troubleshooting)
- [Note e riferimenti](#note-e-riferimenti)

---

## Obiettivi
- Installare e configurare **OpenJDK 21**, **Apache NetBeans**, **JavaFX Scene Builder** e **MySQL (Workbench + Server)**.
- Creare ed eseguire una **Java Application** con **Maven** in NetBeans.
- Comprendere e modificare `pom.xml`; aggiungere una **dipendenza**.
- Usare **Fix Imports**, **Run** e **Debug** in NetBeans.

## Prerequisiti
- Conoscenza base di Java e OOP.
- Connessione internet per scaricare i pacchetti.
- Spazio su disco (~3–5 GB per tool + cache Maven).

> [!TIP]
> Se sei su macOS o Linux, assicurati che `java -version` mostri **21** prima di aprire NetBeans.

---

## Installazione rapida
**1) OpenJDK 21**  
Scarica e installa OpenJDK 21. Durante il setup su Windows, seleziona **Set JAVA_HOME**.

**2) Apache NetBeans**  
Installa l’ultima versione stabile. Al primo avvio, verifica che il JDK predefinito sia 21.

**3) JavaFX Scene Builder**  
Installa Scene Builder; il collegamento a NetBeans verrà mostrato nella Lezione 3.

**4) MySQL (Server + Workbench)**  
Installa e imposta la password per l’utente `root`. Esegui il **test di connessione** in Workbench. 

> [!IMPORTANT]
> Dopo l’installazione, apri un terminale e verifica:
>
> ```bash
> java -version
> mvn -v
> ```

---

## Primo progetto Maven in NetBeans
1. **File → New Project** → *Java with Maven* → *Java Application*.
2. Imposta **GroupId**, **ArtifactId** e **Package** (es. `it.unipi.firstapp`).
3. NetBeans genera un `Main` minimale. Premi **Run**.
4. Aggiungi un **breakpoint** e prova **Debug**.

> [!TIP]
> Tieni d’occhio la finestra **Output** di NetBeans per log e stacktrace.

---

## Maven in breve
Maven gestisce ciclo di vita (compile, test, package), **dipendenze** e **plugin**.  
Il file di configurazione è **`pom.xml`**.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>it.unipi.firstapp</groupId>
  <artifactId>FirstApp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <properties>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
</project>
```

---

## Aggiungere una dipendenza
In NetBeans: apri `pom.xml` → **Add Dependency** → compila *GroupId / ArtifactId / Version* (da **Maven Central**).  
Poi usa **Fix Imports** nel codice per sistemare gli `import`.

> [!TIP]
> Dopo aver modificato il `pom.xml`, esegui un **Build** per forzare il download delle dipendenze.

---

## Esempi pratici

### Logging con Log4j
Aggiungi al `pom.xml`:
```xml
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.23.1</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.23.1</version>
</dependency>
```
Classe `FirstApp.java`:
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class FirstApp {
  private static final Logger log = LogManager.getLogger(FirstApp.class);
  public static void main(String[] args) {
    log.info("Hello from Log4j!");
  }
}
```

> [!NOTE]
> Per configurazioni più avanzate, crea `src/main/resources/log4j2.xml`.

### HTTP Client (Apache HttpClient 5)
Esempio: realizzare una app che scarica e mostra il codice HTML di una pagina web.

Dipendenza nel `pom.xml`:
```xml
<dependency>
  <groupId>org.apache.httpcomponents.client5</groupId>
  <artifactId>httpclient5</artifactId>
  <version>5.3.1</version>
</dependency>
```
Classe di test:
```java
import org.apache.hc.client5.http.classic.methods.HttpGet;
import org.apache.hc.client5.http.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.core5.http.ClassicHttpResponse;

public class FirstApp {
  public static void main(String[] args) throws Exception {
    try (CloseableHttpClient http = HttpClients.createDefault()) {
      ClassicHttpResponse res = (ClassicHttpResponse) http.execute(new HttpGet("https://example.org"));
      System.out.println(res.getCode() + " " + res.getReasonPhrase());
    }
  }
}
```

---

## Comandi utili di Maven (se usato da riga di comando)
```bash
# Compila e scarica le dipendenze
mvn -q compile

# Esegue i test
mvn -q test

# Crea il JAR
mvn -q package

# Esegue la classe main (se configurata)
mvn -q exec:java
```

---

## Troubleshooting
<details>
  <summary><strong>NetBeans non vede il JDK 21</strong></summary>
  In **Tools → Java Platforms**, aggiungi manualmente la cartella del JDK 21.
</details>

<details>
  <summary><strong>Errore su dipendenze Maven</strong></summary>
  Esegui un <code>mvn -U clean package</code> per forzare l'aggiornamento della cache.
</details>

<details>
  <summary><strong>Class not found a runtime</strong></summary>
  Controlla che il plugin <code>exec-maven-plugin</code> sia configurato o crea una configurazione di esecuzione in NetBeans.
</details>

---

## Note e riferimenti
- Questo notebook riassume i contenuti della **Lezione 1** e fornisce esempi pronti all'uso.
- Per dettagli e screenshot di installazione, fare riferimento al materiale della lezione.
