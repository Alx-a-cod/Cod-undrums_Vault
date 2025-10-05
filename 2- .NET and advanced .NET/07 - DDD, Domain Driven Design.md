---
tags:
  - programming_language
  - Data
---

# Domain Driven Design (DDD)

## Concetto
- **Domain Driven Design (DDD)** → approccio alla progettazione software che mette al centro il **dominio del business** e il linguaggio condiviso tra sviluppatori ed esperti di dominio.
- Obiettivo: ridurre il "gap" tra codice e requisiti aziendali.

---

## Concetti chiave
- **Ubiquitous Language** → linguaggio comune tra dev e business, usato ovunque (codice, documenti, conversazioni).
- **Entities** → oggetti con identità univoca (es. `Cliente`, `Ordine`).
- **Value Objects** → oggetti senza identità, definiti dai valori (es. `Indirizzo`, `Denaro`).
- **Aggregates** → gruppi di entità e value objects trattati come un’unica unità di coerenza (es. `Ordine` con le sue `RigheOrdine`).
- **Aggregate Root** → entità principale di un aggregate che gestisce l’accesso e l’integrità (es. `Ordine`).
- **Repositories** → astrazione per accedere agli aggregate dal DB (pattern Repository).
- **Bounded Context** → contesto delimitato in cui un modello di dominio ha un significato preciso.  
  Esempio: "Ordine" nel **contesto logistico** ≠ "Ordine" nel **contesto contabile**.
- **Domain Events** → eventi significativi nel dominio che descrivono qualcosa che è accaduto (es. `OrdineCreato`).
- **Services** → logica di business che non appartiene ad una singola entità/aggregate.

---

## Vantaggi
- Codice più vicino al business.
- Maggiore manutenibilità e scalabilità.
- Chiarezza su responsabilità e confini.

---

## Domande:
1. **Cos’è il Domain Driven Design e perché è utile?**
2. **Qual è la differenza tra Entity e Value Object?**
3. **Puoi spiegare cos’è un Aggregate e perché si usa un Aggregate Root?**
4. **Cos’è un Bounded Context e perché è importante?**
5. **Puoi fare un esempio pratico di Repository Pattern in DDD?**
6. **Come integreresti DDD con Entity Framework o un ORM?**
7. **Hai mai gestito eventi di dominio (Domain Events)?**
8. **Differenza tra logica applicativa e logica di dominio.**
9. **Quando NON ha senso usare DDD (over-engineering)?**

---

## Esempio pratico (semplificato)
```csharp
// Value Object
public record Indirizzo(string Via, string Città, string CAP);

// Entity (Aggregate Root)
public class Ordine
{
    public Guid Id { get; private set; }
    public List<RigaOrdine> Righe { get; private set; } = new();
    public DateTime Data { get; private set; }

    public void AggiungiRiga(string prodotto, int quantità)
    {
        Righe.Add(new RigaOrdine(prodotto, quantità));
    }
}

// Value Object all'interno dell'Aggregate
public record RigaOrdine(string Prodotto, int Quantità);

// Repository
public interface IOrdineRepository
{
    Ordine GetById(Guid id);
    void Save(Ordine ordine);
}
```

## 1. Cos’è il Domain Driven Design e perché è utile?
- **Risposta:**  
Il DDD è un approccio alla progettazione del software che mette al centro il **dominio del business** e il linguaggio condiviso con gli esperti di dominio (**Ubiquitous Language**).  
È utile perché riduce il gap tra codice e requisiti, migliora la manutenibilità, la scalabilità e la coerenza del sistema, specialmente nei **domini complessi**.

---

## 2. Qual è la differenza tra Entity e Value Object?
- **Risposta:**  
- **Entity** → ha un’**identità univoca** che persiste anche se i dati cambiano (es. Cliente con `Id=123`).  
- **Value Object** → non ha identità, è definito solo dai suoi valori (es. Indirizzo "Via Roma 10, Milano").  
Se due Value Object hanno gli stessi valori, sono considerati **uguali**.

---

## 3. Puoi spiegare cos’è un Aggregate e perché si usa un Aggregate Root?
- **Risposta:**  
Un **Aggregate** è un insieme di **Entity** e **Value Object** che formano un’unità di coerenza e consistenza.  
L’**Aggregate Root** è l’entità principale che **controlla l’accesso** al resto dell’aggregate e garantisce l’integrità.  
Esempio: `Ordine` (Aggregate Root) → contiene `RigheOrdine` (Value Objects). Non posso modificare le righe senza passare dall’Ordine.

---

## 4. Cos’è un Bounded Context e perché è importante?
- **Risposta:**  
È un **confine logico** entro il quale un modello di dominio ha un significato ben preciso.  
È importante perché lo stesso concetto può avere significati diversi in contesti diversi.  
Esempio: "Ordine" nel **contesto logistico** = spedizione, nel **contesto contabile** = fatturazione.  
Aiuta a evitare ambiguità e a definire chiaramente le responsabilità dei moduli.

---

## 5. Puoi fare un esempio pratico di Repository Pattern in DDD?
- **Risposta:**  
Il Repository fornisce un’**astrazione** per accedere agli Aggregate dal DB, nascondendo i dettagli tecnici.

```csharp
public interface IOrdineRepository
{
    Ordine GetById(Guid id);
    void Save(Ordine ordine);
}

public class OrdineRepositoryEF : IOrdineRepository
{
    private readonly AppDbContext _context;
    public OrdineRepositoryEF(AppDbContext context) => _context = context;

    public Ordine GetById(Guid id) => _context.Ordini.Include(o => o.Righe).First(o => o.Id == id);

    public void Save(Ordine ordine)
    {
        _context.Update(ordine);
        _context.SaveChanges();
    }
}
```
## 6. Come integreresti DDD con Entity Framework o un ORM?

- **Risposta:**  
    Uso **EF Core** per la persistenza, ma mantengo il **dominio pulito** (Entities, Value Objects, Aggregate).  
    Il Repository traduce tra il **modello di dominio** e il **DB**.
    
- Evito di "inquinare" il dominio con attributi di persistenza.
- Uso `Owned Types` per Value Objects.
- Configuro mapping e relazioni in `DbContext`.

---

## 7. Hai mai gestito eventi di dominio (Domain Events)?

- **Risposta:**  
    Sì, un Domain Event rappresenta un fatto accaduto nel dominio.  
    Esempio: `OrdineCreato`, `PagamentoRicevuto`.  
    Vantaggi: decoupling, possibilità di inviare notifiche o attivare processi senza inserire logica extra nelle Entities.  
    In .NET posso implementarlo con `MediatR` o Event Dispatchers.
    

---

## 8. Differenza tra logica applicativa e logica di dominio.

- **Risposta:**
- **Logica di dominio** → regole di business centrali (es. "un ordine non può essere confermato senza almeno una riga").
- **Logica applicativa** → coordinamento di casi d’uso, orchestrazione di servizi e flussi (es. "invia email dopo la conferma dell’ordine").  
    La logica di dominio vive in Entities/Value Objects/Services di dominio; la logica applicativa nei layer Application/Services.

---

## 9. Quando NON ha senso usare DDD (over-engineering)?

- **Risposta:**  
    DDD è utile in domini complessi, con regole di business intricate e in continua evoluzione.  
    Non ha senso in applicazioni CRUD semplici, dove il dominio è banale e un approccio più leggero (es. ORM diretto con Repository minimo) è sufficiente.  
    In quel caso rischierei solo **over-engineering**.
    

---

# Domanda a trabocchetto tipica

**“Qual è la differenza tra un Aggregate e una Tabella del DB?”**

- **Risposta corretta:** Un Aggregate è un concetto di **dominio**, rappresenta un **cluster di oggetti** coesi dal punto di vista del business. ==Una Tabella è solo una rappresentazione fisica dei dati.== 
- <span style="color: #2d98da">Un Aggregate può mappare a più tabelle</span>
- una tabella può appartenere a **più Aggregate in Bounded Context** diversi.