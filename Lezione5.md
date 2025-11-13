# Lezione 5 — Serializzazione di oggetti in XML e JSON (XStream e Gson)

---

## Serializzazione dei dati

La **serializzazione** è il processo di conversione di oggetti o strutture dati in una sequenza di byte, utile per:
- memorizzare informazioni su file o database;
- trasmettere dati tra processi o host diversi;
- ricostruire l’oggetto originale tramite deserializzazione.

Questo meccanismo è fondamentale nelle applicazioni distribuite, in cui più entità scambiano oggetti complessi attraverso la rete.

---

## Linguaggi di codifica standard

I dati serializzati devono poter essere interpretati da linguaggi e piattaforme diverse.  
Per questo si usano formati di codifica standard come:
- **XML (Extensible Markup Language)**;
- **JSON (JavaScript Object Notation)**.

---

## XML – Extensible Markup Language

XML è un linguaggio testuale gerarchico basato su tag, derivato dai linguaggi di markup per documenti web.  
Definito dal **W3C**, consente di strutturare e rappresentare i dati in modo leggibile e interoperabile.

### Esempio di file XML

```xml
<?xml version="1.0"?>
<business-card>
   <name>
      <title>Ing.</title>
      <firstname>Carlo</firstname>
      <lastname>Vallati</lastname>
   </name>
   <organization>Dip. Ingegneria dell'Informazione</organization>
   <address>
      <street>Via Diotisalvi 2</street>
      <city cap="56122">Pisa</city>
      <country>ITALY</country>
   </address>
   <email>c.vallati@iet.unipi.it</email>
</business-card>
```

- Gli **elementi** rappresentano campi dati (es. `<firstname>`).  
- Gli **attributi** rappresentano proprietà (es. `cap="56122"`).

Gli **XML Schema (XSD)** definiscono la struttura e le regole di validazione dei file XML.

---

## JSON – JavaScript Object Notation

JSON è un formato testuale più compatto e leggibile di XML, basato su coppie chiave–valore.  
È lo standard più diffuso nello scambio di dati tra applicazioni web e mobile.

### Esempio JSON

```json
{
  "business-card": {
    "name": {
      "title": "Ing.",
      "firstname": "Carlo",
      "lastname": "Vallati"
    },
    "organization": "Dip. Ingegneria dell'Informazione",
    "address": {
      "street": "Via Diotisalvi",
      "city": "Pisa",
      "country": "ITALY"
    },
    "email": "c.vallati@iet.unipi.it"
  }
}
```

---

## Librerie Java per la serializzazione

Le due librerie più utilizzate per la serializzazione/deserializzazione in Java sono:

| Formato | Libreria | Pacchetto principale |
|----------|-----------|----------------------|
| XML | XStream | `com.thoughtworks.xstream` |
| JSON | Gson | `com.google.gson` |

---

## Serializzazione e Deserializzazione con Gson (JSON)

### Serializzazione
```java
Gson gson = new Gson();
String json = gson.toJson(c);
```

### Deserializzazione
```java
Gson gson = new Gson();
Credenziali c = gson.fromJson(line, Credenziali.class);
```

Il metodo `fromJson` converte una stringa JSON (`line`) in un’istanza della classe `Credenziali`.

### Esercizio
Modificare l’app e il server per scambiare le **credenziali** in formato JSON invece che testuale.

---

## Serializzazione e Deserializzazione con XStream (XML)

### Serializzazione
```java
XStream xstream = new XStream();
xstream.addPermission(AnyTypePermission.ANY);
xstream.alias("credenziali", Credenziali.class);
String xml = xstream.toXML(c);
```

### Deserializzazione
```java
XStream xstream = new XStream();
xstream.addPermission(AnyTypePermission.ANY);
xstream.alias("credenziali", Credenziali.class);
Credenziali c = (Credenziali) xstream.fromXML(line);
```

### Classe serializzabile
```java
@XStreamAlias("credenziali")
public class Credenziali implements Serializable {

    public String nome;
    public String password;

    public Credenziali(String n, String p){
        nome = n;
        password = p;
    }
}
```

---

## Esercizio – Localizzazione multilingua

Modificare l’applicazione per supportare la **localizzazione** dei testi (ad esempio, pulsanti e messaggi in lingue diverse).

### Passaggi

1. Definire una classe `Linguaggio` con un campo `String` per ogni testo visualizzato.  
2. In `initialize()`, caricare un’istanza della classe da file XML tramite `XStream`.  
3. Aggiornare i testi dei pulsanti usando i valori caricati.  
4. Creare più file XML, ad esempio:  
   - `linguaggio_it.xml` (italiano)  
   - `linguaggio_en.xml` (inglese)  
   da salvare in:  
   ```
   src/main/resources/it/unipi/nomeapp/languages
   ```

### Esempio di caricamento lingua

```java
XStream xstream = new XStream();
xstream.alias("linguaggio", Linguaggio.class);
xstream.addPermission(AnyTypePermission.ANY);

Linguaggio lang = (Linguaggio) xstream.fromXML(
    getClass().getResource("languages/linguaggio_en.xml")
);
```

Dopo il caricamento, aggiornare il testo dei pulsanti:
```java
buttonLogin.setText(lang.loginText);
buttonExit.setText(lang.exitText);
```

---
