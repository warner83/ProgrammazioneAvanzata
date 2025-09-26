# Lezione 2 — GitHub e versionamento del codice (Notebook in stile GitHub)

> Corso: **Programmazione avanzata**  
> Obiettivo: comprendere il versionamento con Git, l’uso di GitHub, clonazione, push, pull e branching.

---

## Introduzione a Git e GitHub

- **Git**: sistema di versionamento distribuito, tiene traccia delle modifiche ai file.  
- **GitHub**: piattaforma online per la collaborazione e l’hosting di repository Git.  
- Obiettivi principali:  
  - Tenere uno storico del codice.  
  - Lavorare in team in modo strutturato.  
  - Gestire branching e merging.

---

## Installazione e setup

1. **Scarica Git** da <https://git-scm.com/downloads>.  
2. Configura l’identità (una volta sola per PC):

```bash
git config --global user.name "Nome Cognome"
git config --global user.email "tuamail@example.com"
```

3. Verifica le impostazioni:

```bash
git config --list
```

---

## Creare un repository

1. **Su GitHub**: crea un nuovo repository (es. `first-repo`).  
2. **Clona in locale**:

```bash
git clone https://github.com/utente/first-repo.git
```

3. Aggiungi file e fai il primo commit:

```bash
echo "# First Repo" >> README.md
git add README.md
git commit -m "primo commit"
git push origin main
```

---

## Flusso di lavoro base

- **Stato repo**:

```bash
git status
```

- **Aggiungere file**:

```bash
git add NomeFile.java
```

- **Commit locale**:

```bash
git commit -m "Messaggio descrittivo"
```

- **Inviare su GitHub**:

```bash
git push origin main
```

- **Aggiornare il codice locale**:

```bash
git pull origin main
```

---

## Branching e merging

1. Creare un branch:

```bash
git checkout -b nuova-feature
```

2. Lavorare sul branch, poi fare commit/push.  
3. Tornare su `main` e unire le modifiche:

```bash
git checkout main
git merge nuova-feature
```

4. Eliminare branch locali inutili:

```bash
git branch -d nuova-feature
```

---

## Esercizi consigliati

1. Configura Git con nome ed email.  
2. Crea un repository su GitHub e clonalo in locale.  
3. Aggiungi un file Java, effettua commit e push.  
4. Prova a creare un branch, modificarlo, unirlo a `main` e fare push finale.

---

> Fonte: appunti della **Lezione 2 — GitHub e versionamento del codice**, corso *Programmazione avanzata*.
