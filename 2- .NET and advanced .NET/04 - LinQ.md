---
tags:
  - csharp
  - programming_language
  - NET
  - SQL
---

### LINQ – Concetti Fondamentali

- **LINQ (Language Integrated Query)** è integrato in .NET e permette di interrogare collezioni di oggetti come se fossero database.  
- Offre due principali sintassi:
  - **Query syntax** → simile a SQL (`from`, `where`, `orderby`, `select`)  
  - **Method syntax** → basata su metodi di estensione (`.Where()`, `.OrderBy()`, `.Select()`)  
- Tipi di esecuzione:
  - **Deferred Execution** → la query viene eseguita solo quando i dati sono richiesti (es. `IEnumerable<T>`).  
  - **Immediate Execution** → la query viene eseguita subito e i risultati vengono memorizzati (es. `.ToList()`, `.ToArray()`).

---

### Casi d’Uso Tipici in Azienda

1. Filtrare dati di una lista:
```csharp
var clientiPremium = clienti
    .Where(c => c.Tipo == "Premium")
    .OrderBy(c => c.Nome)
    .ToList();
```

2. Selezionare solo alcuni campi:
    
```csharp
var nomiClienti = clienti
    .Select(c => c.Nome)
    .ToList();
```

3. Join tra collezioni:
    

```csharp
var ordiniClienti = from o in ordini
                     join c in clienti on o.ClienteID equals c.ID
                     select new { c.Nome, o.Data, o.Totale };
```

4. Aggregazioni:
```csharp
var totaleVendite = ordini.Sum(o => o.Totale);
var mediaOrdini = ordini.Average(o => o.Totale);
```

4. Grouping:
```csharp
var ordiniPerCliente = ordini
    .GroupBy(o => o.ClienteID)
    .Select(g => new { ClienteID = g.Key, Totale = g.Sum(x => x.Totale) })
    .ToList();
```

---

### Esempio di Case Study

> **Scenario**: un’applicazione gestionale che deve mostrare un report dei clienti attivi e dei loro ordini dell’ultimo mese, ordinati per spesa totale.

```csharp
var reportClientiAttivi = clienti
    .Where(c => c.IsActive)
    .Select(c => new
    {
        c.Nome,
        OrdiniRecenti = ordini
            .Where(o => o.ClienteID == c.ID && o.Data >= DateTime.Now.AddMonths(-1))
            .OrderByDescending(o => o.Totale)
            .ToList(),
        TotaleSpesa = ordini
            .Where(o => o.ClienteID == c.ID && o.Data >= DateTime.Now.AddMonths(-1))
            .Sum(o => o.Totale)
    })
    .Where(c => c.OrdiniRecenti.Any())
    .OrderByDescending(c => c.TotaleSpesa)
    .ToList();
```

Punti da evidenziare a colloquio:

- **Deferred vs Immediate Execution** → `.ToList()` forza l’esecuzione immediata, utile per snapshot dei dati.
    
- **Tipizzazione forte** → evita errori runtime e rende il codice più leggibile.
    
- **Chaining di metodi** → permette di scrivere query complesse in modo chiaro e modulare.
    
- **Performance** → filtrare prima di fare `.Select()` e `.ToList()` per ridurre overhead su collezioni grandi.
  
  ### LINQ – Flusso di Esecuzione di una Query (Step by Step)

**Passo 1** – Definizione della Query
- Scrivi la query usando sintassi **Query** o **Method**.  
- La query è solo una **descrizione** di cosa vuoi ottenere, **non viene ancora eseguita** (Deferred Execution).

**Passo 2** – Deferred Execution
- La query rimane **lazily evaluated** finché non richiedi esplicitamente i dati:
- `.ToList()`, `.ToArray()`, `.Count()`, `.First()`, ecc.  
- Questo permette di combinare più filtri e trasformazioni senza eseguire la query più volte.

**Passo 3** – Filtraggio dei Dati
- Viene applicata la clausola `Where()` o `from … where …`.  
- Solo gli elementi che soddisfano il criterio entrano nella fase successiva.

**Passo 4** – Ordinamento
- Applica `OrderBy()` o `orderby` se presente.  
- La collezione filtrata viene ordinata secondo le regole specificate.

**Passo 5** – Proiezione dei Dati
- Applica `Select()` per trasformare gli oggetti in ciò che ti serve (es. solo alcune proprietà).  
- Utile per ridurre l’overhead e trasferire solo i dati necessari.

**Passo 6** – Aggregazioni o Raggruppamenti
- Fasi opzionali: `GroupBy()`, `Sum()`, `Average()`, ecc.  
- Producono valori aggregati o strutture gerarchiche.

**Passo 7** – Materializzazione della Query
- Chiamando `.ToList()` o simili, la query viene **eseguita realmente** e i risultati sono memorizzati in memoria.

**Diagramma concettuale:**
```diff
Query LINQ (Definizione)
        ↓ (Deferred)
Filtraggio → Ordinamento → Proiezione → Aggregazione → Materializzazione (.ToList())
```

---

### Tipi di LINQ

| Tipo | Descrizione | Esecuzione | Use Case |
|------|-------------|------------|----------|
| **LINQ to Objects** | Query su collezioni in memoria (`List<T>`, `Array`, ecc.) | Deferred o Immediate | Manipolazione dati in memoria |
| **LINQ to SQL** | Query direttamente sul database SQL Server | Tradotte in SQL dal provider | Applicazioni legacy o interazione diretta con DB |
| **LINQ to Entities (Entity Framework)** | Query su DbSet del contesto EF | Tradotte in SQL dal provider EF | Applicazioni moderne con ORM |

---

### Ottimizzazione LINQ

1. **Filtrare prima di selezionare** (`Where()` prima di `Select()`) → riduce il numero di elementi da trasformare.
2. **Usare proiezioni mirate** (`Select()` su proprietà necessarie) → evita di caricare oggetti completi inutili.
3. **Evitare query ridondanti** → combinare più operazioni in un’unica query se possibile.
4. **Materializzare la query solo quando serve** → evita di fare `.ToList()` troppo presto, riducendo overhead.
5. **Aggregazioni complesse** → valutare se eseguirle lato database (EF/LINQ to SQL) o in memoria (LINQ to Objects).

---

### Esempio Pratico (Case Study)

**Scenario:** Report clienti attivi e ordini ultimi 30 giorni.

```csharp
var reportClientiAttivi = clienti
    .Where(c => c.IsActive)  // Filtraggio
    .Select(c => new
    {
        c.Nome,
        OrdiniRecenti = ordini
            .Where(o => o.ClienteID == c.ID && o.Data >= DateTime.Now.AddMonths(-1))
            .OrderByDescending(o => o.Totale)
            .ToList(), // Materializzazione interna
        TotaleSpesa = ordini
            .Where(o => o.ClienteID == c.ID && o.Data >= DateTime.Now.AddMonths(-1))
            .Sum(o => o.Totale) // Aggregazione
    })
    .Where(c => c.OrdiniRecenti.Any()) // Filtraggio secondario
    .OrderByDescending(c => c.TotaleSpesa)
    .ToList(); // Materializzazione finale
```

### Punti da saper spiegare:

- Query tipizzata e sicura → nessun cast manuale, errori runtime ridotti.
- Uso combinato di filtri, ordinamenti e aggregazioni.
- Deferred Execution → .ToList() finale esegue tutto insieme.
- Ottimizzazione: filtrare prima, proiezione mirata, evitare query ridondanti.
- Possibilità di tradurlo in LINQ to SQL o LINQ to Entities se il contesto è un database.
  
### Punti chiave LINQ – Come si implementano

#### 1. Query tipizzata e sicura

- In LINQ le query sono fortemente **tipizzate**, quindi il compilatore conosce i tipi degli oggetti e delle proprietà.
- Non servono cast manuali (`(int)`, `(string)`), riducendo gli errori runtime.

- Esempio:
```csharp
var nomiClienti = clienti.Select(c => c.Nome).ToList(); // Nome già stringa, no cast
```

**Beneficio**: errori rilevati a compile-time, autocompletamento in IDE.

#### 2. Uso combinato di filtri, ordinamenti e aggregazioni

Puoi concatenare operazioni come Where(), OrderBy(), Select(), GroupBy() in un’unica query leggibile.

Esempio:

```csharp
var topClienti = clienti
    .Where(c => c.IsActive)
    .OrderByDescending(c => c.TotaleAcquisti)
    .Select(c => new { c.Nome, c.TotaleAcquisti })
    .ToList();
```

**Beneficio**: meno codice ridondante, logica chiara, query ottimizzabile.

#### 3. Deferred Execution

LINQ non esegue subito la query; costruisce un oggetto query che viene valutato solo quando serve.
Trigger per l’esecuzione: .ToList(), .ToArray(), .Count(), .First().

Esempio:

```csharp
var query = clienti.Where(c => c.IsActive); // Non esegue nulla
var listaClienti = query.ToList(); // Qui i dati vengono effettivamente letti
```

**Beneficio**: riduce accessi al DB o in memoria, consente di combinare più filtri prima dell’esecuzione.

#### 4. Ottimizzazione
- Filtrare prima di selezionare: riduce il numero di elementi da elaborare.
- Proiezione mirata: Select() solo sulle proprietà necessarie, riduce l’overhead.
- Evitare query ridondanti: combinare filtri e aggregazioni in una singola query.

Esempio ottimizzato:

```csharp
var report = clienti
    .Where(c => c.IsActive)
    .Select(c => new 
    { 
        c.Nome, 
        TotaleSpesa = ordini.Where(o => o.ClienteID == c.ID).Sum(o => o.Totale)
    })
    .ToList();
```
#### 5. Traduzione in LINQ to SQL o LINQ to Entities

Se la collezione è un **DbSet** o un contesto **LINQ to SQL**, la stessa query LINQ viene tradotta in SQL.

Esempio con Entity Framework:

```csharp
var clientiAttivi = dbContext.Clienti
    .Where(c => c.IsActive)
    .Select(c => new { c.Nome, c.Email })
    .ToList(); // Tradotto in SQL da EF
```

**Beneficio**: query eseguita direttamente sul DB, riducendo il trasferimento di dati in memoria.

