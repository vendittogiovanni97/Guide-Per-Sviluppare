# Panoramica logica del frontend

Questa panoramica ti aiuterà a orientarti rapidamente tra i file e a comprendere le logiche principali del frontend.

## Struttura del progetto

- La cartella `src/app` contiene le pagine principali dell'applicazione, organizzate secondo le route di Next.js.
- La cartella `src/module/common/components` raccoglie i componenti riutilizzabili, come ad esempio `FilePreviewDialog.tsx` che gestisce la visualizzazione dei file in dialog.
- Il file `README.md` fornisce le istruzioni di base per l'avvio e lo sviluppo del progetto.

## Logiche principali

- I componenti React sono scritti in TypeScript e sfruttano le funzionalità di Next.js per la gestione delle pagine e delle API.
- L'interfaccia utente utilizza Material UI per garantire coerenza e accessibilità.
- Le funzioni di caricamento e anteprima dei file sono centralizzate in componenti dedicati per favorire la riusabilità.

## Dettaglio dei file e delle cartelle in `src`

### src/app

- **dashboard/**: contiene le pagine e le funzionalità principali dell'area dashboard, suddivise in sottosezioni come attach, flow, form, gup, subject. Ogni sottocartella rappresenta una macro-funzionalità o modulo.
  - **attach/**: gestione degli allegati e relative funzionalità.
  - **flow/**: gestione dei flussi di lavoro.
  - **form/**: gestione dei moduli e dei documenti associati.
  - **gup/**: gestione utenti, permessi, ruoli e gruppi.
  - **subject/**: gestione delle anagrafiche.
  - **layout.tsx**: layout principale della dashboard.
  - **page.tsx**: entrypoint della dashboard.
- **login/**: pagina di login dell'applicazione.
- **not-found.tsx**: pagina di errore per route non trovate.
- **layout.tsx**: layout generale dell'app.
- **page.tsx**: entrypoint principale dell'applicazione.
- **favicon.ico**: icona dell'applicazione.

### src/module

---

#### agilaeattach

**Gestione degli allegati**

- **AttachModule.tsx**: entrypoint del modulo, coordina la logica di gestione degli allegati.
- **components/**: componenti specifici per la visualizzazione e gestione degli allegati (es. Attach.tsx per upload e gestione, FileDataTable.tsx per la tabella dei file).
- **interfaces/**: definisce le entità e i tipi TypeScript relativi agli allegati (AttachEntity.tsx).
- **services/**: contiene la logica di business e le chiamate API per la gestione degli allegati (AttachService.tsx).

#### agilaeflow

**Gestione dei flussi di lavoro**

- **FlowModule.tsx**: entrypoint del modulo, gestisce la logica dei flussi.
- **components/**: componenti per la visualizzazione e gestione dei flussi (Flow.tsx).
- **interfaces/**: tipi e strutture dati dei flussi (FlowEntity.tsx).
- **services/**: logica di business e chiamate API per i flussi (FlowService.tsx).

#### agilaeform

**Gestione delle form e dei moduli**

- **FormModule.tsx**: entrypoint del modulo, gestisce la logica delle form.
- **components/**: componenti per aggiunta, visualizzazione, modifica e tabella delle form (AddModuleAction.tsx, FormDataModal.tsx, FormDataTable.tsx, FormDataView.tsx, FormStructureTable.tsx).
- **interfaces/**: tipi e strutture dati delle form (FormEntity.tsx, cardDefault.tsx).
- **services/**: logica di business e chiamate API per le form (FormService.ts).

#### agilaegup

**Gestione di gruppi, utenti, permessi e ruoli**

- **GupModule.tsx**: entrypoint del modulo, gestisce la logica di GUP.
- **components/**: componenti per la gestione di account, gruppi, permessi, ruoli e moduli (AccountAction.tsx, AccountModal.tsx, AccountTable.tsx, FunctionTable.tsx, GroupModal.tsx, GroupTable.tsx, ModuleTable.tsx, PermissionTable.tsx, RoleModal.tsx, RoleTable.tsx).
- **interfaces/**: tipi e strutture dati di GUP (GupEntity.tsx).
- **services/**: logica di business e chiamate API per GUP (GupService.tsx).

#### agilaenotify

**Gestione delle notifiche**

- **NotifyModule.tsx**: entrypoint del modulo, gestisce la logica delle notifiche.
- **components/**: componenti per la visualizzazione delle notifiche (Notify.tsx).
- **interfaces/**: tipi e strutture dati delle notifiche (NotifyEntity.tsx).
- **services/**: logica di business e chiamate API per le notifiche (NotifyService.tsx).

#### agilaesubject

**Gestione delle anagrafiche**

- **SubjectModule.tsx**: entrypoint del modulo, gestisce la logica delle anagrafiche.
- **components/**: componenti per la gestione e visualizzazione delle anagrafiche (SubjectModal.tsx, SubjectTable.tsx).
- **interfaces/**: tipi e strutture dati delle anagrafiche (SubjectEntity.tsx).
- **services/**: logica di business e chiamate API per le anagrafiche (SubjectService.tsx).

#### common

**Componenti, hook, provider, servizi, stili e utility comuni**

- **components/**: ampia raccolta di componenti UI generici e riutilizzabili (input, tabelle, dialog, sidebar, navbar, ecc.), inclusi componenti specializzati come FilePreviewDialog.tsx, KanbanBoard.tsx, JsonTreeViewer.tsx, ecc.
- **hooks/**: custom hook condivisi, come useRouterWithWaiting per la gestione avanzata della navigazione.
- **interfaces/**: tipi TypeScript condivisi tra i moduli (AgilaeCard.tsx, AgilaeFile.tsx, AgilaeForm.tsx, Entity.tsx, Module.tsx, ecc.).
- **providers/**: provider di contesto React per autenticazione, tema, gestione moduli (AuthProvider.tsx, ThemeProvider.tsx, ModuleProvider.tsx, ClientOnly.tsx).
- **services/**: servizi comuni per autenticazione, fetch, gestione moduli, logica condivisa (AuthenticationService.ts, CommonService.tsx, FetchService.tsx, ModuleService.tsx).
- **styles/**: stili globali e temi (globals.css, theme.tsx, theme_agilae.tsx, theme_gb.tsx).
- **utils/**: utility generiche per validazione, logger, gestione file, CSS, ecc. (CommonUtils.tsx, CssUtils.tsx, FileUtils.tsx, Logger.tsx, ValidateUtils.tsx).

---

### src/pages

- **api/**: contiene endpoint API custom di Next.js, ad esempio `form_structure.ts` per la gestione della struttura delle form lato server.

### src/structure

- **agilaeform/**, **agilaesubject/**, **list/**: raccolgono file TypeScript che rappresentano strutture dati, test o configurazioni specifiche per i moduli (ad esempio, `FormCertificatoCommon.tsx`, `FormTest.tsx`, `GruppoBonifacio.tsx`).

---

## Come contribuire

- Aggiorna questo file ogni volta che vengono introdotte nuove logiche o componenti rilevanti.
- Per domande o approfondimenti, consulta la documentazione ufficiale di Next.js e Material UI.

---

Aggiungi qui eventuali dettagli aggiuntivi o note utili che emergono durante l'analisi del codice o lo sviluppo.
