# Come scrivere buoni prompt per generare codice con gli LLM
*Traduzione in italiano e adattamento della pagina: “How to write good prompts for generating code from LLMs”. Fonte originale indicata in fondo.*

Aggiornato: aprile 2025 (autore originale: Ayush Thakur).

---

I **Large Language Model (LLM)** stanno rivoluzionando la generazione di codice, ma per ottenere output di qualità è fondamentale creare **prompt efficaci**. La qualità del codice generato dipende direttamente dalla qualità del prompt: un prompt debole porta a risposte generiche o incomplete, mentre uno ben strutturato massimizza il potenziale del modello.

Di seguito sono riportate **strategie pratiche** per scrivere prompt efficaci nella generazione di codice.

## 1. Fornisci contesto dettagliato

La profondità e la qualità del **contesto** che fornisci sono correlate con la pertinenza e l’accuratezza dell’output.

**Elementi chiave da includere:**
- Dominio del problema
- Caratteristiche della codebase esistente
- Vincoli di implementazione
- Requisiti prestazionali
- Pattern architetturali già in uso

Puoi anche usare **@riferimenti** per puntare il modello a file o funzioni specifiche (es. `@authMiddleware.js`, `@userModel.js`) per essere più preciso caricando i files nel sistema.

**Esempio**
- ❌ *Debole*: “Crea un sistema di autenticazione utente.”  
- ✅ *Meglio*: “Crea un sistema di autenticazione JWT per una API Node.js Express che integri la nostra collezione utenti su MongoDB. Deve gestire hashing password con bcrypt, token validi 24 ore e rotazione dei refresh token. Il middleware usa async/await. Vedi `@authMiddleware.js` e `@userModel.js`.”

## 2. Scomponi i problemi in passi

I compiti complessi richiedono una **decomposizione sistematica**:
- Parti da requisiti funzionali chiari.
- Analizza struttura delle directory e organizzazione del codice.
- Guida l’LLM attraverso **passi logici** che rispettino i confini architetturali.

- ❌ *Sbagliato*: “Realizza un'applicazione per la memorizzazione dei libri di una biblioteca.”  
- ✅ *Meglio*: “Crea una classe Java che rappresentano i libri di una biblioteca. -> Crea una schermata JavaFX per mostrare i libri. -> Crea un controllore Spring per esporre i metodi necessari per accedere ai dati dei libri.”

## 3. Sii specifico rispetto ai pattern esistenti

La **specificità tecnica** aumenta la qualità del codice. Riferisciti a pattern, convenzioni di naming, standard e priorità (es. **leggibilità** vs **prestazioni**).

**Esempio**
- ❌ *Debole*: “Scrivi una funzione per processare dati utente.”  
- ✅ *Meglio*: “Crea un metodo nella classe `UserProcessor` (`src/services/UserProcessor.js`) che trasformi i dati utente **seguendo lo stesso approccio funzionale** di `transformPaymentData`. Priorità alla leggibilità.”

## 4. Rigenera invece di fare rollback

Quando il codice generato ha problemi, spesso è meglio **rigenerare** la parte problematica anziché correggerla incrementalmente, per evitare di propagare errori e incorporare nuovi vincoli.

**Esempio**
> “Proviamo un approccio diverso per l’algoritmo di ordinamento. La versione precedente è O(n²). Rigenera con focus su O(n log n) usando un *merge sort* simile agli altri moduli di data processing.”

## 5. Usa la riflessione con approcci multipli

Chiedi al modello **2–3 strategie alternative**, poi domandagli di **valutarne i trade‑off** (complessità temporale/spaziale, leggibilità, manutenibilità) e di selezionare la più adatta.

## 6. Attiva meccanismi di auto‑revisione

Invita il modello a **verificare** il proprio output rispetto a:
- Correttezza logica
- Efficienza
- Gestione edge case
- Sicurezza
- Aderenza ai requisiti

Questo imita *code review* e *analisi statica* all’interno dello stesso ciclo prompt‑risposta.

## 7. Dai una “persona” o un frame di riferimento al modello

Assegna una **persona tecnica** coerente con il compito (es. “**ingegnere backend senior** in sistemi distribuiti”, “**security architect** con esperienza OWASP”, “**DevOps** cloud‑native”, “**frontend UX**/accessibilità”).

**Esempio**
> “Agisci come un **security engineer senior** durante una code review. Crea una registrazione utenti in Python/Django con gestione password, validazione input e difese contro vulnerabilità web comuni.”

## 8. Chiarisci vincoli di linguaggi, framework e librerie

Specifica versioni e dipendenze (es. **Python 3.9**, **FastAPI 0.95** con **Pydantic v2**, **SQLAlchemy 2.0**). Evita che il modello usi feature non disponibili o incompatibili.

**Esempio**
> “Genera un endpoint REST con: Python 3.9, FastAPI 0.95 + Pydantic v2, SQLAlchemy 2.0, JWT con `AuthManager` esistente, compatibile con PostgreSQL 13.”

## 9. Richiedi un ragionamento a catena (chain‑of‑thought)

Per problemi complessi, guida il modello in **stadi sequenziali** (vedi anche suggerimento numero 2):
- Spiegazione concettuale
- Pseudocodice
- Dettagli di implementazione per componente
- Implementazione integrata completa

Questo riduce errori logici e migliora la coerenza.

## 10. Specifica vincoli ed edge case

Elenca a priori **casi limite** e **vincoli** (valori di confine, risorse, condizioni eccezionali) così che il codice includa validazioni, gestione errori e ottimizzazioni adeguate.

**Esempio**
> “Implementa una funzione di elaborazione file che gestisca: file vuoti, file >1GB (a chunk), CSV malformati (log ed elabora le righe valide), accessi concorrenti (locking), interruzioni di rete (resume).”

---

**Conclusione.** L’*art and science* del prompt engineering applicato alla generazione di codice può trasformare gli LLM da generatori “basici” a **partner di sviluppo** capaci di produrre software più robusto, efficiente e manutenibile. 
**Questi non vanno considerati come sostituti dello sviluppatore ma come tool che permettono l'aumento vertiginoso della produttivita'.**

---

*Fonte originale (EN):* https://github.com/potpie-ai/potpie/wiki/How-to-write-good-prompts-for-generating-code-from-LLMs
*Licenza e diritti: contenuto originale su GitHub Wiki; questa è una traduzione ad uso didattico.*
