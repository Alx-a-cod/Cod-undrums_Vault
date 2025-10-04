---
tags:
  - SQL
  - programming_language
---
## Tipi di Database

- **RDBMS (Relational Database Management System)**  
  Organizzano i dati in tabelle (righe e colonne) e permettono di definire relazioni tramite chiavi primarie e chiavi esterne. Utilizzano **SQL** per le operazioni CRUD.
- **NoSQL**  
  Memorizzano i dati in formati diversi dalle tabelle relazionali, come documenti (MongoDB), grafi (Neo4j) o coppie chiave-valore (Redis). Ottimizzati per scalabilità orizzontale.
- **NewSQL** → combina la scalabilità dei NoSQL con la coerenza dei RDBMS (es. CockroachDB).
- **In-memory DB** → dati in RAM per prestazioni elevate (es. Redis, Memcached).

 I database relazionali utilizzano il linguaggio **SQL** (Structured Query Language) per eseguire operazioni di <span style="color: #8392a4">creazione, lettura, aggiornamento e cancellazione</span> (CRUD) sui dati.
 Le operazioni **CRUD** sono fondamentali per la gestione dei dati in un database relazionale, permettendo di creare nuove voci, leggere dati esistenti, aggiornare informazioni e cancellare voci non più necessarie.

---
## Linguaggi SQL

- **DDL** (Data Definition Language) → definizione struttura DB: `CREATE`, `ALTER`, `DROP`.
- **DML** (Data Manipulation Language) → gestione dati: `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
- **DCL** (Data Control Language)→ gestione permessi: `GRANT`, `REVOKE`.
- **TCL** (Transaction Control Language) → gestione transazioni: `COMMIT`, `ROLLBACK`.

>
>L'ordine di lettura di una query SQL è:      **FROM** → **WHERE** → **GROUP BY** → **HAVING** → **SELECT** → **ORDER BY**. 
> I comandi <span style="color: #8392a4">DML vengono eseguiti dopo i comandi DDL</span>, poiché la struttura del ==database deve essere definita prima di poter manipolare i dati al suo interno==.

---

## Transazioni e ACID

Insieme di operazioni che vengono eseguite come un'**unica unità di lavoro**, garantendo che ==tutte le operazioni vengano completate con successo o nessuna di esse venga applicata==.

- Le transazioni sono fondamentali per garantire l'**integrità dei dati** e prevenire situazioni di inconsistenza in caso di errori o interruzioni durante l'esecuzione delle operazioni.

Seguono le proprietà <span style="color: #8392a4">ACID</span> (Atomicità, Consistenza, Isolamento, Durabilità) per garantire che i dati rimangano coerenti e affidabili.

- **ACID**:
  - **A**tomicity → tutte le operazioni all'interno di una transazione devono essere completate con successo, altrimenti nessuna di esse viene applicata.
  - **C**onsistency → una transazione deve portare il database da uno stato valido a un altro stato valido, mantenendo le regole di integrità dei dati.
  - **I**solation → le operazioni all'interno di una transazione devono essere isolate dalle operazioni di altre transazioni, prevenendo interferenze e conflitti.
  - **D**urability → dati persistono dopo commit. Una volta che una transazione è stata completata con successo, le modifiche apportate ai dati devono essere permanenti e resistenti a guasti o interruzioni.
    
- Livelli di isolamento: `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`.

---
## Indici

- è una struttura dati che migliora la velocità delle operazioni di lettura sui dati di una tabella, consentendo un accesso più rapido alle righe in base ai valori di una o più colonne.
  Gli indici funzionano come un elenco ordinato di valori, che permette al database di trovare rapidamente le righe corrispondenti a una determinata condizione di ricerca.
   Migliorano le prestazioni delle query di lettura, ma possono rallentare le operazioni di scrittura (inserimento, aggiornamento, cancellazione)
   a causa della necessità di aggiornare gli indici stessi.
   
   Funziona con esatte copie dei dati, non con riferimenti.
   
- Tipi:
  - **Clustered Index** → determina l’ordine fisico dei dati; 1 per tabella. ==Un indice clustered determina l'ordine fisico delle righe in una tabella.==
  - **Non-clustered Index** → struttura separata; più indici possibili.  ==Un indice non-clustered crea una **struttura separata** che **punta** alle righe della tabella.==
  - **Unique Index** → garantisce valori univoci.
		        Garantisce che i valori nella colonna (o nelle colonne) indicizzate siano **univoci**, evitando duplicati.  
			    Vantaggio: integrità dei dati.  
				Uso tipico: colonne chiave candidate, email, username.
  - **Composite Index** → Indice creato su **più colonne** contemporaneamente.  
				   Vantaggio: migliora le query che filtrano o ordinano sui campi combinati.  
				  Attenzione: l’ordine delle colonne è importante per l’efficacia dell’indice.  
				  Uso tipico: `WHERE Col1 = ? AND Col2 = ?` o ordinamenti su più colonne.
  - **Full-text Index** → Indice ottimizzato per ricerche su **testo libero**, consentendo ricerche più complesse di semplici confronti di stringhe.  
				Supporta query tipo: `CONTAINS`, `FREETEXT`.  
				Vantaggio: ricerca efficiente in grandi quantità di testo.  
				Uso tipico: documenti, descrizioni prodotti, articoli, blog.
    
> `Vantaggi`: accelera `SELECT`.
> `Svantaggi`: rallenta `INSERT/UPDATE/DELETE` perché aggiornare l’indice ha un costo.

## Normalizzazione vs Denormalizzazione

- **Normalizzazione** → riduce ridondanza, migliora integrità.
- **Denormalizzazione** → migliora performance di lettura, aumenta ridondanza (utile in reporting/OLAP).

## Paginazione

- Tecnica per **dividere i risultati in più pagine**, migliorando prestazioni e UX.
- Esempi:
```sql
  SELECT * FROM Tabella 
  ORDER BY ID 
  OFFSET 10 ROWS 
  FETCH NEXT 10 
  ROWS ONLY;
```

---

## Eventual Consistency

- **Definizione**: modello di consistenza tipico dei sistemi distribuiti (NoSQL, cloud DB), dove i dati **non sono immediatamente coerenti** su tutti i nodi, ma lo diventano dopo un certo periodo di tempo.  
- **Vantaggi**: alta disponibilità e scalabilità orizzontale.  
- **Svantaggi**: possibile lettura di dati "vecchi" prima della convergenza.  
- **Uso tipico**: sistemi di caching distribuito, applicazioni altamente scalabili (es. Amazon DynamoDB, Cassandra).

---

# Ottimizzazione Query, DB, e Indici

- **Obiettivi**: ridurre tempi di risposta e carico server.
  
- **Strategie**:
  
  1. Indici appropriati
  2.  Paginazione dei risultati
  3. **Parametrizzazione** → evita SQL injection e riuso dei piani di esecuzione.
  4. **Query efficienti**.
  5. **Analisi piano esecuzione** → `EXPLAIN` o `SHOW PLAN` per valutare costi operazioni.

## Ottimizzazione query SQL
### Principi generali

- Utilizzare **indici appropriati** su colonne usate in WHERE, JOIN, ORDER BY.  
- Evitare `SELECT *`; selezionare solo le colonne necessarie.  
- Filtrare i dati **prima possibile** con clausole WHERE.  
- Evitare funzioni sulle colonne in WHERE (impediscono l’uso degli indici).  
- Usare JOIN appropriate e preferire JOIN/CTE a subquery nidificate.  
- Ottimizzare GROUP BY e HAVING solo se necessario.  

### Performance e gestione dati

- Limitare le righe restituite con **TOP/LIMIT** o paginazione.  
- Evitare cursori: prediligere operazioni **set-based**.  
- Aggiornare regolarmente **statistiche e indici**.  
- Considerare partizionamento, denormalizzazione o sharding per grandi dataset.  
- Valutare l’uso di viste materializzate o indici avanzati (es. columnstore).  

### Scrittura e manutenzione

- Ottimizzare inserimenti, aggiornamenti e cancellazioni per ridurre impatto sugli indici.  
- Usare UNION ALL invece di UNION quando possibile.  
- Evitare CROSS JOIN inutili.  
- Applicare compressione dati se le tabelle sono grandi.  

### Monitoraggio e best practice

- Analizzare le query con **Execution Plans**, Profiler o strumenti del DBMS.  
- Usare **caching** lato applicazione o database per query frequenti.  
- Collaborare con DB admin e team sviluppo per risolvere colli di bottiglia.  
- Documentare e condividere le best practice interne.  
- Formare il team sulle ultime tecniche di ottimizzazione SQL.  

### Nota
- Per dataset molto grandi o esigenze particolari, valutare anche **soluzioni NoSQL** o alternative al modello relazionale.

---

## Alternative lato client all'ottimizzazione DB:

### Tecniche di Caricamento Dati Lato Client

Quando gestiamo grandi quantità di dati, il caricamento completo può rallentare l’applicazione. Esistono diverse strategie per migliorare l’esperienza utente e le performance:

#### 1. Infinite Scroll
- Caricamento dinamico dei dati **man mano che l’utente scorre** la pagina.
- Vantaggi:
  - Esperienza utente fluida.
  - Nessun pulsante di “pagina successiva”.
- Svantaggi:
  - Difficile tornare a posizioni precedenti.
  - Può aumentare l’utilizzo di memoria lato client.
- Uso tipico: feed social, timeline, cataloghi prodotti.

#### 2. Lazy Loading
- I dati o componenti **vengono caricati solo quando servono**, non subito.
- Esempi:
  - Immagini caricate solo quando visibili.
  - Componenti UI caricati solo su interazione.
- Vantaggi:
  - Riduce il tempo di caricamento iniziale.
  - Risparmio banda e memoria.
- Svantaggi:
  - Maggiore complessità lato codice.
  
#### 3. Caricamento via API REST in blocchi (Chunked Fetch)
- La pagina fa **richieste successive al server** per ottenere solo un sottoinsieme di dati.
- Esempio:
  - REST endpoint: `/products?offset=0&limit=20`
  - Il client richiede i dati successivi solo quando necessario.
- <span style="color: #20bf6b">Vantaggi</span>
  - Controllo sul volume di dati trasferiti.
  - Migliora performance lato server e client.
- Svantaggi:
  - Richiede gestione lato client di offset/pagina.
  - Necessità di gestire possibili dati duplicati o pagine mancanti.

### Sintesi
| Tecnica                  | Quando usarla                | Pro                                 | Contro                                 |
|---------------------------|-----------------------------|------------------------------------|----------------------------------------|
| Infinite Scroll           | Liste lunghe, feed          | Esperienza fluida                  | Navigazione difficile                  |
| Lazy Loading              | Contenuti pesanti, immagini | Caricamento rapido                  | Maggior complessità codice             |
| API REST in blocchi       | Paginazione dati da DB      | Controllo traffico e performance   | Gestione pagine lato client            |
## Ottimizzazione e Best Practice per Database SQL

### 1. Ottimizzazione generale del Database
- **Progettazione efficiente**: normalizzazione per ridurre ridondanza e garantire integrità.  
- **Indici appropriati**: sulle colonne usate frequentemente in WHERE, JOIN, ORDER BY.  
- **Statistiche aggiornate**: permettono al query optimizer di scegliere i piani migliori.  
- **Backup e disaster recovery**: politiche regolari per garantire disponibilità e sicurezza.  
- **Ottimizzazione operazioni di scrittura**: minimizzare l’impatto sugli indici e sulle performance.  
- **Partizionamento dati**: utile su tabelle grandi per query più veloci e gestione scalabile.  
- **Caching**: riduce il carico sul DB per query frequenti (memcached, Redis, ecc.).  
- **Visti materializzati**: accelerano query complesse eseguite spesso.  
- **Collaborazione con DBA**: configurazioni ottimali di memoria, CPU e storage.

### 2. Query ricorsive e cicliche
- Utilizzate per strutture gerarchiche (es. dipendenti-manager, directory).  
- Implementazione in SQL tramite **CTE ricorsive** (`WITH RECURSIVE` in standard SQL, `WITH` in SQL Server).  
- Devono avere **condizione di terminazione** per evitare loop infiniti.  
- Attenzione a **limiti di ricorsione** del DB (es. `MAXRECURSION` in SQL Server).  
- **Prestazioni**: possono essere più lente delle query non ricorsive; valutare alternative come tabelle temporanee o elaborazioni lato applicazione.  

#### Esempio pratico (gerarchia dipendenti-manager):
```sql
WITH EmployeeCTE AS (
    SELECT EmployeeID, ManagerID, Name
    FROM Employees
    WHERE ManagerID IS NULL           -- top-level manager
UNION ALL
    SELECT e.EmployeeID, e.ManagerID, e.Name
    FROM Employees e
    JOIN EmployeeCTE cte ON e.ManagerID = cte.EmployeeID
)
SELECT * FROM EmployeeCTE;
```

 In questo esempio, la CTE EmployeeCTE si **richiama ricorsivamente** per ottenere tutti i dipendenti e i loro manager, partendo dai manager di livello superiore (quelli senza un ManagerID).
 La **condizione di terminazione** è definita nella prima parte della CTE, che seleziona i manager di livello superiore.
 Per ogni iterazione, la seconda parte della CTE unisce la tabella Employees con la CTE stessa per trovare i dipendenti che riportano ai manager già selezionati.
 
Alla fine, la query seleziona tutti i record dalla CTE, restituendo l'intera struttura gerarchica dei dipendenti e dei loro manager.

 Le query ricorsive possono essere utili per risolvere problemi complessi, ma è importante considerare le **prestazioni** e la **complessità** del problema prima di utilizzarle.
 In alcuni casi, potrebbe essere più efficiente utilizzare <span style="color: #8392a4">approcci alternativi</span>, come l'uso di <span style="color: #0fb9b1">tabelle temporanee o l'elaborazione dei dati a livello di applicazione</span>.
 Le query ricorsive possono essere limitate dal **numero massimo di ricorsioni** consentite dal database, quindi è importante monitorare e gestire attentamente l'uso delle query ricorsive per evitare problemi di prestazioni o errori di esecuzione.

## Piano di Esecuzione SQL (Execution Plan) – SSMS

### Definizione
- Il **piano di esecuzione** mostra le operazioni che il motore SQL esegue per soddisfare una query.  
- Fornisce informazioni su:
  - **Scansioni di tabelle** (Table Scan, Index Scan, Index Seek)  
  - **Join** (Nested Loops, Merge Join, Hash Join)  
  - **Filtri** e **ordinamenti**  
  - **Costi**: CPU, I/O, memoria  

### Tipologie di Execution Plan
1. **Piano stimato (Estimated Execution Plan)**  
   - Generato prima dell’esecuzione della query  
   - Prevede le operazioni che il motore SQL eseguirà  
   - Utile per identificare potenziali inefficienze senza eseguire la query  
2. **Piano effettivo (Actual Execution Plan)**  
   - Generato dopo l’esecuzione della query  
   - Mostra le operazioni realmente eseguite  
   - Può differire dal piano stimato a causa di:
     - Statistiche non aggiornate  
     - Distribuzione dei dati  
     - Indici disponibili  

### Analisi e Ottimizzazione
- Confrontare **piano stimato vs effettivo** per identificare discrepanze  
- Individuare **colli di bottiglia** nelle query  
- Analizzare **scansioni costose** e operazioni di join inefficaci  
- Ottimizzare query tramite:
  - Indici appropriati  
  - Riscrittura della query  
  - Partizionamento tabelle  
  - Caching  

### Strumenti e Best Practice
- **SQL Server Management Studio (SSMS)**: `Ctrl + M` per includere il piano di esecuzione  
- Possibilità di salvare e condividere i piani per la collaborazione  
- Strumenti avanzati di terze parti:
  - **SQL Sentry Plan Explorer**  
  - **Redgate SQL Monitor**  
- Monitorare costantemente le query più eseguite per migliorare le prestazioni generali del DB  

### SQL Server – Execution Plan e Ottimizzazione Query

---

#### 1. Scansioni, Join e Costi di Esecuzione
- **Table Scan**: lettura di tutte le righe di una tabella → costoso su tabelle grandi, indica possibile mancanza di indice.  
- **Index Scan**: lettura di tutte le righe di un indice → più efficiente di Table Scan.  
- **Index Seek**: utilizzo mirato dell’indice → accesso più efficiente alle righe necessarie.  

**Tipi di Join**:
- Nested Loops Join → efficiente per piccole tabelle o quando è presente un indice.  
- Merge Join → richiede tabelle ordinate, utile per join su colonne indicizzate.  
- Hash Join → per tabelle grandi senza ordine, costruisce una tabella hash per trovare corrispondenze.  

**Costi di esecuzione**:
- Ogni operazione ha un costo stimato in CPU, I/O e memoria.  
- Analizzando i costi relativi si individuano i **colli di bottiglia**.

---

#### 2. Piano stimato vs Piano effettivo
- **Piano stimato**: generato prima dell’esecuzione → prevede la strategia del motore.  
- **Piano effettivo**: generato dopo l’esecuzione → mostra cosa è stato realmente eseguito.  
- Importanza: confrontando i due piani si verificano eventuali discrepanze e si ottimizzano le query.

---

#### 3. Colli di Bottiglia e Ottimizzazione
- **Come riconoscerli**: operazioni con costi elevati, Table Scan su tabelle grandi, join non indicizzati.  
- **Strategie di ottimizzazione**:
  1. Creare indici appropriati (clustered, non-clustered, columnstore).  
  2. Riscrivere query riducendo subquery o join superflui.  
  3. Applicare partizionamento o query paginate per grandi dataset.  
  4. Implementare caching per dati frequentemente richiesti.

---

#### 4. Strumenti SSMS e avanzati
- **SSMS**:
  - `Ctrl + M` → Include Actual Execution Plan.  
  - Pianificazione grafica per sequenze di operazioni, join e scansioni.  
- **Strumenti avanzati**:
  - SQL Sentry Plan Explorer → analisi dettagliata dei piani, evidenzia colli di bottiglia.  
  - Redgate SQL Monitor → monitoraggio continuo delle query e delle performance.

---

#### 5. Ottimizzazione lato Query e Database
- **Query**:
  - Evitare `SELECT *` → specificare solo le colonne necessarie.  
  - Filtrare subito con `WHERE`.  
  - Usare `TOP` o `OFFSET/FETCH` per la paginazione.  
  - Evitare cursori → preferire operazioni **set-based**.

- **Database**:
  - Aggiornare regolarmente statistiche e indici.  
  - Monitorare con Execution Plans e DMV (Dynamic Management Views).  
  - Applicare partizionamento e viste materializzate per query complesse.

- **Lato client/server**:
  - Paginazione e lazy loading → migliorano la responsività del client.  
  - Ridurre carico server → evitare grandi trasferimenti di dati in un’unica query.

---

## LIKE 

 LIKE è un operatore SQL utilizzato nelle clausole WHERE per **cercare un modello specifico** all'interno di una colonna di testo.
LIKE utilizza i caratteri jolly **%** e _ per rappresentare rispettivamente una sequenza di caratteri di lunghezza variabile e un singolo carattere.

- Se **nome%** significa che il campo nome inizia con "nome" e può essere seguito da qualsiasi sequenza di caratteri.
-  se **%nome** significa che il campo nome termina con "nome" e può essere preceduto da qualsiasi sequenza di caratteri.
-  se **%nome%** significa che il campo nome contiene "nome" in qualsiasi posizione all'interno del testo.

---

## Per resettare l'indice di una tabella: <span style="color: #2d98da">Truncate</span>
```sql
TRUNCATE table NomeTabella;
```
 TRUNCATE TABLE rimuove tutti i record da una tabella in modo rapido ed efficiente, resettando anche gli indici associati alla tabella.
A differenza di DELETE, TRUNCATE TABLE non registra la rimozione di ogni singolo record nel log delle transazioni, rendendolo più veloce per eliminare grandi quantità di dati.
Tuttavia, TRUNCATE TABLE non attiva i trigger associati alla tabella e non può essere utilizzato se la tabella è referenziata da una chiave esterna.
Inoltre, TRUNCATE TABLE non può essere annullato, quindi è importante utilizzarlo con cautela.
