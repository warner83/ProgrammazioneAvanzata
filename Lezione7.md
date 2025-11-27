# Lezione 7 — Creazione di un’applicazione Web utilizzando Spring 

---

## 1. Il framework Spring

Spring è un framework opensource per applicazioni Web basate su Java EE.  
Offre diversi moduli, tra cui:

- **Spring Boot** – crea automaticamente lo scheletro di un progetto  
- **Spring MVC** – gestisce richieste HTTP e routing  
- **Spring Data JPA** – semplifica l'interazione con database  

---

## 2. Creazione progetto con Spring Boot

Per generare lo scheletro dell’applicazione:

1. Visitare **https://start.spring.io**
2. Impostare:
   - Project: **Maven**
   - Java: **21**
3. Aggiungere dipendenze:
   - Spring Web
   - Spring Boot DevTools
   - Thymeleaf
4. Scaricare lo **ZIP** generato.

---

## 3. Importazione in NetBeans

1. NetBeans → File → Import Project → *From ZIP*  
2. Importare lo ZIP scaricato.  
3. Eseguire **Build** per scaricare le dipendenze.  
4. Impostare la Main Class del progetto:

```
PrimaAppWebApplication
```

(Generata automaticamente da Spring Boot).

---

## 4. Errori comuni

### Su macOS (Apple Silicon) e Linux
Errore eseguendo `mvnw`. Soluzione:

```bash
chmod +x mvnw
```

### Su Windows (nome utente con spazio)
Modificare il file `mvnw.cmd`, riga 43:

❌ Errato  
```
%MAVEN_JAVA_EXE% %WRAPPER_LAUNCHER% %MAVEN_CMD_LINE_ARGS%
```

✅ Corretto  
```
"%MAVEN_JAVA_EXE%" %WRAPPER_LAUNCHER% %MAVEN_CMD_LINE_ARGS%
```

---

## 5. Architettura Spring MVC

- Le richieste arrivano al **Front Controller** di Spring.  
- Vengono inoltrate al **Controller Java** definito dal programmatore.  
- Il controller restituisce un **template HTML (Thymeleaf)**.  
- Il front controller costruisce la risposta per il browser.

---

## 6. Creare il primo Controller

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "home";
    }
}
```

I template HTML devono trovarsi in:
```
src/main/resources/templates
```

---

## 7. Template “home.html”

```html
<html>
  <head>
    <title>Home</title>
  </head>
  <body>
    <h1>Benvenuto nella prima app Spring!</h1>
  </body>
</html>
```

Aprire: **http://localhost:8080/**

---

## 8. Risorse statiche (immagini)

Creare la cartella:
```
src/main/resources/static/img/
```

E richiamare l’immagine nel template:

```html
<img th:src="@{/img/pizza.png}"/>
```

---

## 9. Sistema ordini: scambio dati browser ↔ app

### Classe Pizza (JavaBean)

```java
public class Pizza implements Serializable {
    private String id;
    private String name;
    private Type type;

    public Pizza() {}

    public Pizza(String id, String name, Type type) {
        this.id = id;
        this.name = name;
        this.type = type;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public Type getType() { return type; }

    public void setId(String id) { this.id = id; }
    public void setName(String name) { this.name = name; }
    public void setType(Type type) { this.type = type; }

    public static enum Type { BIANCA, ROSSA, VEGETARIANA };
}
```

---

## 10. Lista pizze nel controller

```java
private static List<Pizza> pizze = Arrays.asList(
    new Pizza("MRG","Margherita",Type.ROSSA),
    new Pizza("BNG","Bianca",Type.BIANCA),
    new Pizza("BCP","Bianca con pomodorini",Type.BIANCA),
    new Pizza("BFL","Bufala",Type.ROSSA),
    new Pizza("VRG","Verdurosa",Type.VEGETARIANA)
);

@GetMapping("/")
public String home(Model model) {
    model.addAttribute("tutte", pizze);
    return "home";
}
```

---

## 11. Template home.html con selezione pizza

```html
<h1>Scegli la tua pizza</h1>

<form method="POST">
  <select id="selezione" name="selezione">
    <div th:each="p : ${tutte}">
      <option th:value="${p.id}" th:text="${p.name}"></option>
    </div>
  </select>

  <button>Invia pizza scelta</button>
</form>
```

---

## 12. Handler POST (scelta pizza)

```java
@PostMapping("/")
public String selezionata(Model model, @RequestParam("selezione") String selezione) {
    logger.info("Pizza selezionata: " + selezione);
    model.addAttribute("indirizzo", new Indirizzo());
    return "indirizzo";
}
```

---

## 13. Classe Indirizzo

```java
public class Indirizzo {
    private String nome;
    private String cognome;

    public Indirizzo() {}

    public Indirizzo(String nome, String cognome) {
        this.nome = nome;
        this.cognome = cognome;
    }

    public String getNome() { return nome; }
    public String getCognome() { return cognome; }

    public void setNome(String nome) { this.nome = nome; }
    public void setCognome(String cognome) { this.cognome = cognome; }
}
```

---

## 14. Template indirizzo.html

```html
<h1>Fornisci il tuo indirizzo</h1>

<form method="POST" th:action="@{/conferma}">
  <label>Nome:</label>
  <input type="text" th:field="${indirizzo.nome}"/>

  <label>Cognome:</label>
  <input type="text" th:field="${indirizzo.cognome}"/>

  <button>Conferma identità</button>
</form>
```

---

## 15. Handler POST /conferma

```java
@PostMapping("/conferma")
public String conferma(Indirizzo i) {
    logger.info("Indirizzo: " + i);
    return "conferma";
}
```

---

## 16. Template conferma.html

```html
<html>
  <head>
    <title>Ordina la tua pizza</title>
  </head>
  <body>
    <h1>Benvenuto</h1>
    <img th:src="@{/img/pizza.png}"/>
    <h1>Ordine confermato</h1>
  </body>
</html>
```

---

## 17. Esercizio finale

- Aggiungere pagina indirizzo  
- Aggiungere pagina conferma ordine

---

> Contenuto tratto fedelmente da *Lezione 7.docx*.
