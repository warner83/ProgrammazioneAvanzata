# Lezione 9 — Realizzazione di servizi Web con Spring Boot. Interazione con database tramite JPA e Hibernate  

---

## 1. Introduzione

L’obiettivo della lezione è costruire un **servizio Web REST** utilizzando **Spring Boot**, che permetta di:

- esporre dati in formato JSON o XML;
- leggere e scrivere dati da/verso un database MySQL tramite **Spring Data JPA**;
- estendere il servizio con metodi aggiuntivi (ricerca, inserimento, aggiornamento);
- comprendere come utilizzare **Hibernate** in progetti non‑Spring.

---

## 2. Configurazione del progetto Spring Boot

Spring Boot crea automaticamente la struttura di un progetto Web e gestisce le dipendenze principali.

### File `application.properties`

Per permettere all’applicazione di connettersi a MySQL:

```
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/drivers
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# spring.jpa.show-sql=true
```

⚠️ Nota: Spring Boot include una dipendenza MySQL non corretta.  
Sostituire:

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

con:

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

---

## 3. Definizione della classe Entity (Driver)

La classe **Driver** rappresenta la tabella del database.  
Spring Data JPA utilizza annotazioni per collegare i campi alla struttura SQL.

```java
import jakarta.persistence.*;

@Entity(name = "drivers")
@Table(name = "drivers")
public class Driver {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private int id;

    @Column(name = "name")
    private String name;

    @Column(name = "lastname")
    private String lastName;

    @Column(name = "nationality")
    private String nationality;

    @Column(name = "birthdate")
    @Temporal(TemporalType.DATE)
    private Date birthDate;

    // Getters e setters...
}
```

### Spiegazione
- `@Entity` → indica che la classe è un'entità gestita da JPA.  
- `@Table` → nome della tabella reale.  
- `@Id` e `@GeneratedValue` → chiave primaria auto‑incrementale.  
- `@Temporal` → specifica il formato della data.

---

## 4. Repository: accesso ai dati

Spring Data JPA genera automaticamente query a partire dai nomi dei metodi.

```java
public interface DriverRepository extends CrudRepository<Driver, Integer> {
    Driver findByName(String n);
}
```

Questo metodo viene **auto‑implementato** da Spring: crea una query SQL che cerca per il campo `name`.

---

## 5. Creazione dei servizi REST nel Controller

### 5.1 Recupero di tutti i driver

```java
@GetMapping(path="/all")
public @ResponseBody Iterable<Driver> getAllUsers() {
    return driverRepository.findAll();
}
```

Spring converte automaticamente la lista in JSON.  
Per restituire XML:

```java
@GetMapping(path="/all", produces = MediaType.APPLICATION_XML_VALUE)
public @ResponseBody Iterable<Driver> getAllUsers() {
    return driverRepository.findAll();
}
```

Per usare XML serve aggiungere la dipendenza:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```

---

### 5.2 Ricerca per nome

```java
@PostMapping(path="/driver")
public @ResponseBody Driver getUserByName(@RequestParam String name) {
    return driverRepository.findByName(name);
}
```

Test con `curl`:

```
curl localhost:8080/drivers/driver -d name=Carlo
```

---

### 5.3 Aggiungere un nuovo driver

```java
@PostMapping(path="/add")
public @ResponseBody String addNewDriver(@RequestBody Driver d) {
    driverRepository.save(d);
    return "Saved";
}
```

Test con `curl`:

```
curl localhost:8080/drivers/add -H "Content-Type: application/json" -X POST --data "{ "name": "Carlos", "lastName": "Saintz", "nationality": "Spagna", "birthDate": "1992-03-12" }"
```

---

## 6. ESERCIZIO — estendere il servizio web

Richiesto:

1. Implementare nuovi metodi REST.
2. Aggiungere una funzione per inserire nuovi piloti.
3. Implementare la ricerca per nome.
4. Verificare via browser o `curl`.

---

## 7. Uso di Hibernate senza Spring (JPI/Hibernate)

Per progetti Java non‑Spring si può usare Hibernate manualmente.

### Dipendenza Maven

```xml
<dependency>
  <groupId>org.hibernate.orm</groupId>
  <artifactId>hibernate-core</artifactId>
  <version>6.1.3.Final</version>
</dependency>
```

Se il progetto usa moduli Java, aggiungere:

```java
requires jakarta.persistence;
requires org.hibernate.orm.core;
requires java.naming;
```

---

## 8. File `hibernate.cfg.xml`

```xml
<?xml version = "1.0" encoding = "utf-8"?>
<hibernate-configuration>
    <session-factory>

        <!-- Set URL -->
        <property name="hibernate.connection.url">
            jdbc:mysql://localhost:3306/drivers
        </property>

        <!-- Set User Name -->
        <property name="hibernate.connection.username">root</property>

        <!-- Set Password -->
        <property name="hibernate.connection.password">root</property>

        <!-- Set Driver Name -->
        <property name="hibernate.connection.driver_class">
            com.mysql.jdbc.Driver
        </property>

        <property name="hibernate.show_sql">true</property>

    </session-factory>
</hibernate-configuration>
```

---

## 9. Avviare Hibernate e interagire con il DB

```java
Configuration configuration = new Configuration();
configuration.configure("hibernate.cfg.xml");
configuration.addAnnotatedClass(Driver.class);

SessionFactory sessionFactory = configuration.buildSessionFactory();
Session session = sessionFactory.openSession();
```

### Scrittura nel database

```java
Driver nd = new Driver(...);
session.save(nd);
session.getTransaction().commit();
```

### Lettura con HQL

```java
Query query = session.createQuery("FROM drivers", Driver.class);
List<Driver> dr = query.getResultList();
```

---

## 10. Esercizio finale

Implementare:

- ricerca per nome;
- inserimento nuovi piloti;
- esposizione dei dati in JSON o XML;
- configurazione Hibernate per applicazioni non‑Spring.

---


