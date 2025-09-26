# Lezione 2 — Tool di sviluppo avanzati: GIT, GitLab, Log4j 

> Corso: **Programmazione avanzata**  
> Obiettivo: introdurre il controllo di versione con Git, comprendere il flusso di lavoro con repository locali e remoti, sperimentare GitLab e l’integrazione dei test automatizzati, configurare il logging con Log4j.

---

## Introduzione al controllo di versione

Un sistema di controllo versione permette di:
- Tenere traccia della storia dello sviluppo di un progetto.
- Gestire le modifiche effettuate da più sviluppatori.
- Ripristinare versioni precedenti in caso di bug.
- Risolvere i conflitti dovuti a modifiche simultanee sullo stesso file.

Il codice e le sue versioni sono memorizzati in un repository (server). Gli sviluppatori scaricano, modificano e caricano le nuove versioni nel repository.

### Git
- Tool di controllo versione distribuito: gestisce più repository.
- Necessita almeno di:
  - Repository locale (sul PC).
  - Repository remoto (su server GitLab/GitHub).

### Piattaforme
- GitHub o GitLab (in questa lezione si usa GitLab).

---

## Aree di lavoro in Git

1. Working directory: tutti i file del progetto (anche temporanei).
2. Staging area: sottoinsieme dei file destinati al commit.
3. Repository (locale/remoto): archivio ufficiale delle versioni.

---

## Operazioni di base in Git

Aggiungere file alla staging area:
```bash
git add NomeFile.java
```

Creare un commit (repository locale):
```bash
git commit -m "Messaggio descrittivo"
```

Caricare le modifiche sul repository remoto:
```bash
git push origin main
```

Aggiornare il repository locale con quello remoto:
```bash
git pull origin main
```

Clonare un repository remoto:
```bash
git clone https://example.com/utente/progetto.git
```

---

## Operazioni Git in NetBeans

- Initialize Git Repository per inizializzare un repository locale da un progetto esistente.
- Git -> Remote -> Push per collegare e inviare il codice al repository remoto.
- GitLab fornisce anche un editor web per modifiche rapide; dopo un commit via web, usare Git -> Remote -> Pull in NetBeans per aggiornare il locale.

---

## Unit test con Maven

- Maven genera una classe di test con funzioni annotate con `@Test`.
- Ogni test fallito deve invocare `fail()`.
- I test sono eseguiti automaticamente durante la compilazione.

Esercizio: creare una funzione nella classe principale, scrivere il test corrispondente e lanciare Maven per verificarne l’esecuzione.

---

## CI/CD con GitLab

- I repository centralizzati possono eseguire test automatici ad ogni push (**CI/CD**).
- Pipeline tipica:
  1. Build: `mvn compile`
  2. Test: `mvn test`
  3. Deploy: rilascio (opzionale)

Nel file `.gitlab-ci.yml` usare un’immagine Maven (es. `maven:latest`) e definire gli stage `build` e `test`. Ogni commit della pipeline attiva l’esecuzione automatica dei job.

Esercizio: creare un repository su GitLab, aggiungere il progetto con almeno un test Maven e configurare una pipeline CI/CD minima.

---

## Log4j: logging configurabile

Il logging tramite semplici `System.out.println` non è adatto a progetti di dimensioni medio-grandi. È preferibile una libreria configurabile come **Log4j 2**.

### Dipendenze Maven
Aggiungere al `pom.xml`:
```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.16.0</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.16.0</version>
</dependency>
```

### File di configurazione `log4j2.xml`
Creare `src/main/resources/log4j2.xml` con un setup console+file:
```xml
<Configuration status="INFO">
    <Appenders>
        <Console name="ConsoleAppender" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} -  %msg%n" />
        </Console>
        <File name="FileAppender" fileName="application-${date:yyyyMMdd}.log" immediateFlush="false" append="true">
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </File>
    </Appenders>
    <Loggers>
        <Root level="debug">
            <AppenderRef ref="ConsoleAppender" />
            <AppenderRef ref="FileAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

Se la cartella `resources` non è presente, crearla manualmente.

### Uso nel codice Java
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class FirstApp {
    private static final Logger logger = LogManager.getLogger(FirstApp.class);

    public static void main(String[] args) {
        logger.trace("Trace message");
        logger.debug("Debug message");
        logger.info("Info message");
        logger.warn("Warn message");
        logger.error("Error message");
        logger.fatal("Fatal message");
    }
}
```

### Progetti modulari (`module-info.java`)
Nei progetti modulari può essere necessario aggiungere le **requires** per Log4j dentro `module-info.java` (se presente).

Esercizio: modificare il programma per usare la libreria Log4j e verificare la scrittura sia su console sia sul file `application-YYYYMMDD.log`.

---

Fonti: appunti della Lezione 2 — Tool di sviluppo avanzati: GIT, GitLab, Log4j.
