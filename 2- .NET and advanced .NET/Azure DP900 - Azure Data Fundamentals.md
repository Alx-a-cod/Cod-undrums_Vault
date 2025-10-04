---
tags:
  - Azure_Data
  - Data
---

## Concetti principali

- **Tipi di dati**
  
  - **Relazionali**: tabelle con schema rigido (es. SQL Server, Azure SQL Database).
  - **Non relazionali**: dati non strutturati o semi-strutturati (JSON, chiave-valore, documenti, grafi).
  - **Transazionali (OLTP)**: ottimizzati per scrittura/aggiornamento rapido.
  - **Analitici (OLAP)**: ottimizzati per query complesse e analisi (es. Data Warehouse).

- **Modelli di dati**
  
  - **Relazionale**: tabelle, chiavi primarie/esterne, normalizzazione.
  - **Non-relazionale**: key-value (Redis), documenti (Cosmos DB), grafi (Gremlin).

## Servizi Azure più importanti

- **Azure SQL Database**: database relazionale PaaS.
- **Azure Synapse Analytics**: data warehouse per analisi su larga scala.
- **Cosmos DB**: database multi-modello NoSQL (documenti, grafi, chiave-valore, colonne).
- **Azure Data Lake Storage (ADLS)**: storage per big data non strutturati.
- **Azure Databricks**: ambiente per analisi big data e AI.
- **Azure Stream Analytics**: analisi in tempo reale su flussi di dati.
- **Event Hub**: ingestione di eventi ad alta velocità.

## Concetti di base

- **Scalabilità**: verticale (aumentare risorse) vs orizzontale (aggiungere nodi).
- **Alta disponibilità**: replica, failover, geo-distribuzione.
- **Sicurezza**: autenticazione, autorizzazione (RBAC), cifratura.
- **Backup e Disaster Recovery**: RPO, RTO.
- **Data Ingestion**: pipeline per acquisire dati (es. Azure Data Factory).
- **Data Visualization**: Power BI collegato ad Azure.

## Conoscenze utili: 

- Differenza **IaaS, PaaS, SaaS** nel contesto dati (VM con SQL vs Azure SQL vs Power BI).
- Differenza **batch vs streaming**.
- Capire quando usare **relazionale vs NoSQL**.
- Sapere cos’è **serverless** (es. Azure SQL Serverless, Cosmos autoscale).
- Essere in grado di fare esempi pratici:
  - "Per un gestionale uso **Azure SQL Database**."
  - "Per dati non strutturati e distribuiti a livello globale, uso **Cosmos DB**."
  - "Per BI aziendale, costruisco pipeline con **Data Factory** → salvo in **Data Lake** → analizzo in **Synapse** → visualizzo con **Power BI**."
    

## IaaS vs PaaS vs SaaS (nel contesto dati)

- **IaaS (Infrastructure as a Service)**  

  - Fornisce macchine virtuali e risorse base.  
  - Sei responsabile di OS, DB, patching.  
  - **Esempio**: installo SQL Server su una VM in Azure.  
  - Pro: controllo totale. Contro: gestione complessa.  

- **PaaS (Platform as a Service)**  

  - Il provider gestisce OS, runtime e database engine.  
  - Tu gestisci solo i dati e lo schema.  
  - **Esempio**: uso Azure SQL Database (non gestisco patch, backup, HA).  
  - Pro: meno manutenzione. Contro: meno personalizzazione.  

- **SaaS (Software as a Service)**  

  - Servizio pronto, solo da usare.  
  - Non gestisci né piattaforma né DB.  
  - **Esempio**: Power BI come servizio per reportistica.  
  - Pro: zero manutenzione. Contro: dipendenza dal fornitore.  

---

## Batch vs Streaming

- **Batch Processing**  

  - Elaborazione di grandi quantità di dati in blocco.  
  - Ritardo accettabile (es. ore).  
  - **Esempio**: caricamento giornaliero di file CSV in Data Lake, elaborati da Synapse.  

- **Streaming Processing**  

  - Elaborazione in tempo reale o near real-time.  
  - Dati processati appena arrivano.  
  - **Esempio**: Azure Stream Analytics su eventi IoT (sensori che inviano dati).  

---

## Relazionale vs NoSQL

- **Relazionale (SQL)**  

  - Schema rigido (tabelle, relazioni).  
  - Ottimo per dati strutturati e transazioni (OLTP).  
  - **Esempio**: gestionale ordini/clienti → Azure SQL Database.  

- **NoSQL**  

  - Flessibile, scalabile, distribuito.  
  - Tipi: documento, chiave-valore, grafo, colonna.  
  - Ottimo per dati semi-strutturati o globali.  
  - **Esempio**: social network con milioni di utenti e contenuti → Cosmos DB.  

---

## Serverless

- **Concetto**: non gestisci istanze o capacità; il servizio scala automaticamente.  
- Paghi solo l’uso effettivo.  
- **Esempi**:  
  - Azure SQL Database Serverless (si sospende quando inattivo, riparte on demand).  
  - Cosmos DB autoscale (scala automaticamente in base al throughput).  

---

## Esempi pratici da citare

- **Gestionale aziendale (OLTP)** → Azure SQL Database
- **App mobile globale (dati JSON distribuiti worldwide)** → Cosmos DB
- **Business Intelligence** → Data Factory → Data Lake → Synapse → Power BI
- **IoT / log real-time** → Event Hub → Stream Analytics → Power BI dashboard


