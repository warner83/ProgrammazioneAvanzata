# Conversation Report: SQL and JavaFX Styling

## Conversation Transcript

### User:

Act as SQL expert. I have a database with a table with the following structure:

```sql
CREATE TABLE books (
  book_name         TEXT        NOT NULL,
  author            TEXT        NOT NULL,
  printing_company  TEXT,
  year_of_release   SMALLINT    CHECK (year_of_release BETWEEN 1450 AND (EXTRACT(YEAR FROM CURRENT_DATE)::INT + 1)),
  number_of_pages   INTEGER     NOT NULL CHECK (number_of_pages > 0),
  topic             TEXT,
  summary           TEXT,
  for_under_18      BOOLEAN     NOT NULL DEFAULT FALSE
);
```

Create a query to return all the books published from 2017 and 2022 that are for under 18, do not contain the word 'bridge' on the title and have less than 100 pages

### Assistant:

Got it! Based on your table structure, here’s an SQL query that meets all your criteria:

```sql
SELECT *
FROM books
WHERE year_of_release BETWEEN 2017 AND 2022
  AND for_under_18 = TRUE
  AND number_of_pages < 100
  AND book_name NOT ILIKE '%bridge%';
```

---

### User:

Act as Java developer. I have the following fxml descriptor for an interface of a JavaFX application:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import java.lang.*?>
<?import javafx.geometry.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>
<?import javafx.scene.layout.VBox?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.control.Button?>
<?import javafx.geometry.Insets?>

<VBox alignment="CENTER" spacing="20.0" xmlns="http://javafx.com/javafx/8" xmlns:fx="http://javafx.com/fxml/1" fx:controller="it.unipi.secondaappinterfaccia.PrimaryController">
   <children>
      <Label text="Primary View" />
      <Button fx:id="syncDataButton" onAction="#syncData" text="Sincronizza dati" />
      <Button fx:id="mostraDati" mnemonicParsing="false" onAction="#mostra" text="Mostra" />
   </children>
   <padding>
      <Insets bottom="20.0" left="20.0" right="20.0" top="20.0" />
   </padding>
</VBox>
```

Change the style of the interface following the bootstrap style. Change the background color in light blue. Change the font to Comic. Don't use external files. 

### Assistant:

```xml
<VBox alignment="CENTER" spacing="20.0"
      xmlns="http://javafx.com/javafx/8"
      xmlns:fx="http://javafx.com/fxml/1"
      fx:controller="it.unipi.secondaappinterfaccia.PrimaryController"
      style="-fx-background-color: #ADD8E6; -fx-font-family: 'Comic Sans MS'; -fx-font-size: 14px;">
   
   <children>
      <Label text="Primary View" 
             style="-fx-font-size: 18px; -fx-font-weight: bold;" />
      
      <Button fx:id="syncDataButton" onAction="#syncData" text="Sincronizza dati" 
              style="-fx-background-color: #007bff; -fx-text-fill: white; -fx-padding: 8 15 8 15; -fx-background-radius: 5; -fx-font-weight: bold;" />
      
      <Button fx:id="mostraDati" mnemonicParsing="false" onAction="#mostra" text="Mostra" 
              style="-fx-background-color: #007bff; -fx-text-fill: white; -fx-padding: 8 15 8 15; -fx-background-radius: 5; -fx-font-weight: bold;" />
   </children>
   
   <padding>
      <Insets bottom="20.0" left="20.0" right="20.0" top="20.0" />
   </padding>
</VBox>
```

This sets the background, font, and button styles without any external CSS file.
