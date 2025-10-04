---
tags:
  - csharp
  - NET
  - SQL
---
### ENTITY FRAMEWORK CORE (EF Core) – Concetti chiave

#### 1. Cos’è EF Core
- EF Core è un **Object-Relational Mapper (ORM)** open-source per .NET.
- Permette di lavorare con database relazionali usando **oggetti C#**, evitando di scrivere SQL manuale.
- Supporta database come **SQL Server, SQLite, PostgreSQL, MySQL**.
- Offre funzionalità avanzate:
  - **Tracciamento delle modifiche** (Change Tracking)
  - **Migrazioni del database**
  - **Lazy Loading** dei dati

---

#### 2. Approcci di modellazione dei dati
- **Code First**: definisci classi C#, EF genera il database.
- **Database First**: parti da un database esistente, EF genera le classi C#.
- **Model First** (meno comune): designer visivo genera database e classi.
- Conoscere queste modalità è importante per capire come progettare applicazioni scalabili.

---

#### 3. Connessione con LINQ
- EF Core utilizza **LINQ to Entities** per tradurre query LINQ in SQL.
- Differenze rispetto a LINQ to Objects:
  - Esecuzione **Deferred** finché non chiami `.ToList()`, `.First()`, ecc.
  - Alcune funzioni C# potrebbero non essere traducibili in SQL → runtime exception.
- Esempio pratico:
```csharp
var clientiAttivi = dbContext.Clienti
    .Where(c => c.IsActive)
    .OrderByDescending(c => c.TotaleAcquisti)
    .Select(c => new { c.Nome, c.TotaleAcquisti })
    .ToList();
```

#### 4. Tipi di caricamento dei dati

- **Eager Loading**: carica i dati correlati subito con `.Include()`.
    
```csharp
var ordini = dbContext.Clienti.Include(c => c.Ordini).ToList();
```

- **Lazy Loading**: carica dati correlati solo quando acceduti.

- **Explicit Loading**: carica dati correlati manualmente con `.Load()`.
  
  #### 5. Migrazioni e gestione schema

- Permettono di aggiornare il database senza perdere dati.
    
- Comandi tipici:
    
```terminal 
dotnet ef migrations add NomeMigrazione dotnet ef database update
```
    
- Consente **versioning** e **tracciabilità** dello schema.
    

---

#### 6. Performance e ottimizzazione

- **Filtrare prima di includere** dati (`Where()` prima di `Include()`).
    
- **Proiezioni mirate** con `Select()` per limitare i campi caricati.
    
- **No tracking queries** se non serve aggiornare i dati:
    
```csharp
 var clienti = dbContext.Clienti.AsNoTracking().ToList();
```
    
- **Batching e paginazione** per gestire grandi quantità di dati.
    

---

#### 7. Altri ORM e contesti

- **Dapper**: micro-ORM, più vicino al SQL, più veloce ma meno funzionalità.
- **NHibernate**: ORM maturo, simile a Hibernate (Java).
- **Sequelize**: Node.js
- **Hibernate**: Java

---

#### 8. Versioni LTS

- **LTS (Long-Term Support)** = versione supportata più a lungo con aggiornamenti di sicurezza.
- Rilevante per stabilità aziendale: e si preferisce EF Core LTS in progetti enterprise.

---

#### 9. Esempio avanzato (case study)

- Immagina un **e-commerce**:
    

```csharp
var reportVendite = dbContext.Ordini
    .Where(o => o.Data >= startDate && o.Data <= endDate)
    .Include(o => o.Cliente)
    .GroupBy(o => o.ClienteID)
    .Select(g => new {
        Cliente = g.First().Cliente.Nome,
        TotaleAcquisti = g.Sum(o => o.Totale)
    })
    .OrderByDescending(r => r.TotaleAcquisti)
    .ToList();
```

- Mostra: **LINQ to Entities, Include, filtraggio, proiezione e aggregazione**, tutto ottimizzato per EF Core.
  
## EF Core – Punti chiave
#### 1. Code First vs Database First
- **Code First**
  - Scrivi classi C# → EF genera il database.
  - Utile quando inizi un nuovo progetto o vuoi controllo totale sul modello.
  - Permette migrazioni facili e versioning dello schema.
- **Database First**
  - Parti da un database esistente → EF genera classi C#.
  - Utile se il database è già definito o condiviso con altri sistemi.
  - Meno flessibile per modifiche future, ma rapido per iniziare.

---

#### 2. Deferred Execution e LINQ to Entities
- **Deferred Execution**
  - La query viene definita ma **non eseguita subito**.
  - Viene eseguita solo quando accedi ai dati: `.ToList()`, `.First()`, `.Count()`.
- **LINQ to Entities**
  - Traduce query LINQ in SQL.
  - Permette filtraggio, ordinamento, aggregazioni direttamente sul database.
  - Alcune funzioni C# non traducibili → errori runtime.
- **Esempio**
```csharp
var clientiAttivi = dbContext.Clienti
    .Where(c => c.IsActive)
    .Select(c => new { c.Nome, c.Email }); // query definita ma non eseguita
var lista = clientiAttivi.ToList(); // query eseguita qui
```
#### 3. Tipi di caricamento dati

- **Eager Loading**

    - Carica dati correlati subito con `.Include()`.
    - Utile se sai che serviranno tutti i dati correlati.
        
- **Lazy Loading**
    
    - Carica dati correlati solo quando richiesti.
    - Può generare molte query aggiuntive (N+1 problem).
        
- **Explicit Loading**
    
    - Caricamento manuale tramite `.Load()` quando serve.
        
- **Esempio**
    

```csharp
// Eager
var ordini = dbContext.Clienti.Include(c => c.Ordini).ToList();

// Lazy (deve essere abilitato)
var ordiniLazy = dbContext.Clienti.ToList();
var firstOrdini = ordiniLazy[0].Ordini; // qui viene eseguita query aggiuntiva

// Explicit
var cliente = dbContext.Clienti.First();
dbContext.Entry(cliente).Collection(c => c.Ordini).Load();
```

---

#### 4. Ottimizzazione

- **Filtrare prima di includere** → `.Where()` prima di `.Include()`.
- **Proiezione mirata** → `.Select()` per limitare colonne.
- **Paginazione** → `.Skip()` e `.Take()` per grandi dataset.
- **No Tracking** → `.AsNoTracking()` se non serve modificare dati.
- **Evita query ridondanti** → combina filtri, proiezioni e aggregazioni nella stessa query.

---

#### 5. Comparazione EF Core vs Dapper/NHibernate

|Feature|EF Core|Dapper|NHibernate|
|---|---|---|---|
|Livello di astrazione|Alto (ORM completo)|Basso (micro-ORM)|Alto (ORM completo)|
|Sintassi LINQ|Sì|No|Sì|
|Performance|Buona, ma overhead più alto|Molto veloce, vicino al SQL|Simile a EF Core|
|Migrazioni/schema|Sì (Code First)|No|Sì|
|Complessità|Media|Bassa|Alta|

- **Quando usare EF Core:** sviluppo rapido, modelli complessi, tracciamento modifiche.
- **Quando usare Dapper:** performance elevate, query SQL ottimizzate, microservizi.
- **Quando usare NHibernate:** legacy, progetti grandi e complessi con mapping avanzati.
