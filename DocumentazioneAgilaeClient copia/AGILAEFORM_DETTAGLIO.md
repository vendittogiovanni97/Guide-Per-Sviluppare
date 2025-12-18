# Dettaglio modulo AgilaeForm

Questo documento fornisce una panoramica approfondita del modulo **AgilaeForm**, illustrando la sua logica, le funzionalità principali, la struttura dei file e i flussi dati coinvolti.

---

## 1. Scopo del modulo AgilaeForm

Il modulo **AgilaeForm** si occupa della gestione delle form e dei moduli all'interno dell'applicazione. Permette di creare, visualizzare, modificare e gestire i dati relativi ai moduli, integrandosi con altri moduli come allegati, utenti e notifiche.

---

## 2. Struttura dei file

La struttura tipica del modulo è la seguente:

- **FormModule.tsx**: entrypoint del modulo, gestisce la logica principale e coordina i componenti.
- **components/**: contiene i componenti React specifici per la gestione delle form:
  - `AddModuleAction.tsx`: componente per l'aggiunta di nuove azioni/moduli.
  - `FormDataModal.tsx`: dialog/modal per la visualizzazione e modifica dei dati di una form.
  - `FormDataTable.tsx`: tabella che mostra l'elenco delle form esistenti.
  - `FormDataView.tsx`: visualizzazione dettagliata di una singola form.
  - `FormStructureTable.tsx`: tabella che rappresenta la struttura delle form.
- **interfaces/**: definisce i tipi TypeScript e le entità utilizzate dal modulo (es. `FormEntity.tsx`, `cardDefault.tsx`).
- **services/**: contiene la logica di business e le chiamate API per la gestione delle form (`FormService.ts`).

---

## 3. Flusso dati principale

1. **Visualizzazione**: Il componente `FormDataTable.tsx` recupera la lista delle form tramite il servizio e la mostra in tabella.
2. **Aggiunta/Modifica**: Tramite `AddModuleAction.tsx` e `FormDataModal.tsx` l’utente può aggiungere o modificare una form. I dati vengono inviati al backend tramite `FormService.ts`.
3. **Visualizzazione dettagliata**: `FormDataView.tsx` permette di vedere tutti i dettagli di una singola form, inclusi eventuali allegati o dati correlati.
4. **Gestione struttura**: `FormStructureTable.tsx` consente di visualizzare e modificare la struttura delle form (campi, tipi, validazioni).

---

## 4. Peculiarità e logiche avanzate

- **Tipizzazione forte**: Tutti i dati sono tipizzati tramite interfacce TypeScript per garantire sicurezza e coerenza.
- **Componenti riutilizzabili**: I componenti sono progettati per essere riutilizzati anche in altri moduli.
- **Integrazione con Material UI**: L’interfaccia sfrutta i componenti Material UI per una UX coerente e accessibile.
- **Gestione centralizzata delle API**: Tutte le chiamate verso il backend sono incapsulate nei servizi, facilitando la manutenzione e l’evoluzione del codice.

---

## 4.1 Gestione degli switch (checkbox/toggle)

Nel modulo **AgilaeForm**, la gestione degli switch (tipicamente rappresentati da checkbox o toggle) si basa su una struttura dati che definisce i campi del form. Ogni campo di tipo switch viene identificato nella struttura e renderizzato tramite un componente specifico (ad esempio `AgilaeCheckBox`). Quando l'utente interagisce con lo switch, viene aggiornato lo stato locale dei dati del form tramite una funzione di handler (ad esempio `handleChange`). Questo handler aggiorna il valore booleano associato al campo nello stato del form e, se necessario, attiva eventuali logiche condizionali (come mostrare o nascondere altri campi o triggerare validazioni).

Gli switch possono essere influenzati da permessi utente o condizioni di visibilità definite nella struttura del form, garantendo che siano abilitati/disabilitati o visibili solo in determinati contesti. Questo approccio assicura flessibilità e sicurezza nella gestione dei dati e nell'esperienza utente.

---

## 5. Esempio di utilizzo

Per aggiungere una nuova form:

1. L’utente clicca su “Aggiungi” nella tabella delle form.
2. Si apre il `FormDataModal.tsx` dove inserisce i dati.
3. Alla conferma, il modulo invia i dati tramite `FormService.ts`.
4. La tabella si aggiorna mostrando la nuova form.

---

## 6. Dipendenze e collegamenti

- **Altri moduli**: Il modulo può interagire con allegati, utenti e notifiche.
- **Common**: Utilizza componenti, hook e servizi comuni per la gestione di dialog, validazioni, fetch, ecc.

---

## 7. Note di sviluppo

- Aggiornare questo file ogni volta che vengono introdotte nuove logiche o componenti rilevanti.
- Per approfondimenti, consultare la documentazione ufficiale di Next.js e Material UI.

---

Per domande o suggerimenti, rivolgersi al team di sviluppo frontend.
