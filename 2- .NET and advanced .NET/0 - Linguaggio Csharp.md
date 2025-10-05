---
tags:
  - csharp
  - programming_language
  - NET
---

Repository per raccogliere conoscenza di elementi base (e poco più) del linguaggio e della programmazione a oggetti (OOP)
E quindi…

## Cos’è una Classe

Una **classe** in C# è un **modello** <span style="color: #8392a4">(blueprint)</span> che definisce:
- **Dati** → tramite **campi** e **proprietà**
- **Comportamenti** → tramite **metodi**

👉 La classe è un costrutto tipico della [[01 - Programmazione orientata agli oggetti (OOP)]]
Esempio:

```csharp
public class Automobile
{
    private string marca; // campo privato

    public string Modello { get; set; } // proprietà pubblica

    // costruttore
    public Automobile(string marca, string modello)
    {
        this.marca = marca;
        this.Modello = modello;
    }

    // metodo
    public void Accelera()
    {
        Console.WriteLine($"{marca} {Modello} sta accelerando!");
    }
}

/// Uso del metodo:

`var auto = new Automobile("Fiat", "Panda"); auto.Accelera();  // Output: Fiat Panda sta accelerando!`

```
## Principi OOP (Object-Oriented Programming)

1. [[1.a Incapsulamento (Encapsulation)]]
    - Nascondere i dettagli interni
    - Esporre solo ciò che serve (campi privati + proprietà pubbliche)
        
2. [[1.b Ereditarietà]]
    - Possibilità di riusare/estendere una classe base

  ```csharp
    public class Veicolo 
    { 
    public int Ruote { get; set; } 
    } 
    
    public class Moto : Veicolo { }
    ```
    
3. [[1.c Polimorfismo]]
    - Stesso metodo → comportamenti diversi
    
    ```csharp
    public class Veicolo  
    {      
    public virtual void Muovi() = > Console.WriteLine("Veicolo in movimento"); 
    } 
    public class Auto : Veicolo  
    {      
    public override void Muovi() = > Console.WriteLine("L'auto sta guidando"); 
    }
    ```
    
4. [[1.d Astrazione]]
    
    - Definire il _cosa_ deve fare un oggetto, senza definire il _come_
    - Implementato con **interfacce** o **classi astratte**.
      
## Costruttori e distruttori in Csharp
      
#### 1. Costruttore
Il **costruttore** è un metodo speciale che ha: 
-  lo **stesso nome della classe** 
  - nessun **tipo di ritorno** (nemmeno `void`).  
Viene eseguito automaticamente quando viene creata una nuova istanza di una classe.  

`-->` Serve a **inizializzare lo stato dell’oggetto**.
##### 1.a Caratteristiche

- Può essere **overloadato** (ovvero posso ==sovraccaricarlo con più costruttori con parametri diversi==).  
- Se non ne dichiari uno, il <span style="color: #8392a4">compilatore crea un costruttore di default</span> (senza parametri).  

Può essere:
  - **pubblico** → normale creazione oggetti.
  - **privato** → usato in Singleton o factory pattern.
  
Supporta anche i **costruttori statici**:
  - Vengono chiamati <span style="color: #8392a4">una sola volta per tipo</span>, prima di qualsiasi istanza o accesso a membri statici.
  - Non accettano parametri.

##### ex. Esempio di costruttori
```csharp

public class Persona
{
    public string Nome { get; set; }
    public int Eta { get; set; }

    // Costruttore di default
    public Persona()
    {
        Nome = "Sconosciuto";
        Eta = 0;
    }

    // Costruttore con parametri (overloading)
    public Persona(string nome, int eta)
    {
        Nome = nome;
        Eta = eta;
    }

    // Costruttore statico
    static Persona()
    {
        Console.WriteLine("Costruttore statico chiamato una sola volta.");
    }
}

class Program
{
    static void Main()
    {
        var p1 = new Persona();                 // Usa costruttore di default
        var p2 = new Persona("Luca", 25);       // Usa costruttore con parametri

        Console.WriteLine($"{p1.Nome}, {p1.Eta}"); // Sconosciuto, 0
        Console.WriteLine($"{p2.Nome}, {p2.Eta}"); // Luca, 25
    }
}
```
#### 2. Distruttore
Il distruttore è un **metodo speciale** che viene chiamato quando l’**oggetto viene distrutto dal Garbage Collector** (GC).
Serve a <span style="color: #8392a4">rilasciare risorse non gestite</span> (es. file, connessioni, handle di sistema).
##### 2.a Caratteristiche

- Ha lo ==stesso nome della classe preceduto da ~.==
- **Non accetta parametri** e **non ha modificatori di accesso**.
- Non si chiama manualmente → viene invocato dal GC.

In pratica, in C# ==i distruttori si usano raramente, perché è meglio implementare l’interfaccia IDisposable con il pattern Dispose==.

```csharp
public class FileManager
{
    private string filePath;

    public FileManager(string path)
    {
        filePath = path;
        Console.WriteLine($"Aperto file: {filePath}");
    }

    // Distruttore
    ~FileManager()
    {
        Console.WriteLine($"Rilasciate risorse per file: {filePath}");
    }
}

class Program
{
    static void Main()
    {
        var fm = new FileManager("dati.txt");

        // Quando il Garbage Collector ripulisce l’oggetto,
        // verrà invocato il distruttore.
    }
}
```

#### 3. Il pattern consigliato in .NET → IDisposable

Invece di affidarsi al distruttore, ==in .NET si preferisce il Dispose Pattern con l’interfaccia IDisposable==.
Questo consente di **liberare le risorse deterministicamente** (cioè quando voglio io).

```csharp
public class Risorsa : IDisposable
{
    public void Usa()
    {
        Console.WriteLine("Uso la risorsa...");
    }

    public void Dispose()
    {
        Console.WriteLine("Rilascio risorsa!");
    }
}

class Program
{
    static void Main()
    {
        using (var r = new Risorsa()) // Dispose chiamato automaticamente
        {
            r.Usa();
        } // Qui Dispose viene eseguito
    }
}
```

##### Sintesi: 

**Costruttore**: metodo speciale per inizializzare un oggetto. Può essere overloadato e statico.
**Distruttore**: metodo speciale invocato dal GC per liberare risorse non gestite, ma poco usato perché in .NET è preferibile il pattern IDisposable.
>**In Java** non esiste il distruttore esplicito.
>- Java usa il **Garbage Collector**, che libera la memoria automaticamente.
>- posso sovrascrivere il metodo `finalize()` (anche se ormai è deprecato).

<span style="color: #8392a4">Best practice:</span> per risorse critiche (DB, file, socket) si usa Dispose con using.

## Cos’è una **struct**?

Una **struct** (abbreviazione di _structure_) è un tipo di dato (**tipo valore**) che permette di raggruppare dati correlati sotto un unico tipo.  
È simile a una **classe**, ma con differenze importanti in termini di comportamento e gestione della memoria.

---
#### Caratteristiche generali

- Sono **value type** (memorizzate nello _stack_, non nell’heap).
- Non supportano l’**ereditarietà** (ma possono implementare interfacce).
- Possono avere **metodi, proprietà, costruttori** (ma non distruttori).
- Non hanno un **costruttore di default definibile** (il compilatore ne fornisce uno che inizializza i campi a default).
- Sono più **leggere** delle classi, ideali per rappresentare **piccoli oggetti immutabili** (es. coordinate, punti, date, valori numerici composti).
- I campi possono avere tipi diversi.
- Supporta **incapsulamento**, proprietà e metodi (in linguaggi come C#).

`-->` [[1.f Heap, Stack e Value vs Reference Type in C#]]

---

#### Esempio in **C# (struct moderna, simile a classe)**

```csharp
using System;  

// Definizione di una struct
public struct Point
{
    public int X { get; }
    public int Y { get; }

    // Costruttore
    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    // Metodo
    public double DistanceFromOrigin()
    {
        return Math.Sqrt(X * X + Y * Y);
    }
}

// Uso
class Program
{
    static void Main()
    {
        Point p1 = new Point(3, 4);
        Point p2 = p1;   // Viene COPIATO, non referenziato

        Console.WriteLine(p1.DistanceFromOrigin()); // 5
        Console.WriteLine(p2.X); // 3

        // Modificando p2, p1 resta invariato (perché struct è Value Type)
    }
}
```

 In C# le `struct` possono avere:

- campi
- proprietà
- costruttori
- metodi : ma **non possono ereditare da altre classi** (solo implementare interfacce).
---

### Differenze chiave tra **classe** e **struct**

(variano un po’ tra linguaggi, ma in generale):

| Caratteristica                                             | <span style="color: #8392a4">Class (Classe)</span> | <span style="color: #8392a4">Struct</span>         |
| ---------------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| <span style="color: #8392a4">Tipo</span>                   | Reference Type                                     | Value Type                                         |
| <span style="color: #8392a4">Memoria</span>                | Heap (col puntatore sullo stack)                   | Stack (copia diretta dei dati)                     |
| <span style="color: #8392a4">Ereditarietà</span>           | Supportata (eccetto sealed)                        | Non supportata (solo interfacce)                   |
| <span style="color: #8392a4">Costruttore di default</span> | Può essere definito                                | Non definibile, fornito automaticamente            |
| <span style="color: #8392a4">Distruttore</span>            | Supportato                                         | Non supportato                                     |
| <span style="color: #8392a4">Prestazioni</span>            | Più lenta per oggetti piccoli (allocazione heap)   | Più veloce per oggetti piccoli (allocazione stack) |
| <span style="color: #8392a4">Mutabilità</span>             | Tipicamente mutabili                               | Consigliato usarle come immutabili                 |
| Quando usarle                                              | Oggetti complessi con comportamento e stato        | Dati semplici e leggeri (es. `Point`, `DateTime`)  |
|                                                            |                                                    |                                                    |
per linguaggio invece vale:

|Linguaggio|Classe|Struct|
|---|---|---|
|**C**|non esiste|solo contenitori di dati|
|**C++**|tipo riferimento, membri private di default|uguale alla classe, ma membri public di default|
|**C#**|tipo riferimento (heap)|tipo valore (stack)|
|**Java**|solo classi|(non esistono struct, ma si usano classi leggere o `record`)|

---
#### In sintesi:  
- Una **classe** è un **reference type**, supporta ereditarietà e viene gestita sull’heap.    
- Una **struct** è un **value type**, più leggera, senza ereditarietà, adatta a piccoli contenitori di dati.
    
> Usare **struct** quando rappresenti **valori semplici e immutabili**; usare **classi** per **oggetti complessi e con logica**.

`-->` [[1.g Tipi Valore vs Tipi Riferimento]]


## Cos’è una **tuple**?

Una **tuple** in C# è un **contenitore leggero** che permette di raggruppare **più valori di tipi diversi** in un’unica struttura senza dover creare una classe dedicata.

---
### Caratteristiche principali

- Possono contenere **da 1 a 8 elementi** (oltre 8 si annidano automaticamente).  
- Possono essere **nominate** o usare i nomi predefiniti `Item1`, `Item2`, ecc.  
- Sono **immutabili** per impostazione predefinita (i valori non possono cambiare dopo la creazione).  
- Introdotte ufficialmente in **C# 7.0** con sintassi moderna `(tipo1, tipo2, ...)`.  

<span style="color: #e5a216">Esempio di tuple semplice:</span>

```csharp
class Program
{
    static void Main()
    {
        // Tuple non nominata
        var persona = Tuple.Create("Luca", 25, "Roma");
        Console.WriteLine(persona.Item1); // Luca
        Console.WriteLine(persona.Item2); // 25

        // Tuple moderna con nomi
        (string Nome, int Eta, string Citta) persona2 = ("Maria", 30, "Milano");
        Console.WriteLine(persona2.Nome);  // Maria
        Console.WriteLine(persona2.Eta);   // 30
        Console.WriteLine(persona2.Citta); // Milano
    }
}
```

### Tuple come ritorno di metodo

Molto utile quando vuoi ritornare più valori da un metodo senza creare una classe dedicata.

```csharp
public static (int Somma, int Differenza) Calcola(int a, int b)
{
    return (a + b, a - b);
}

class Program
{
    static void Main()
    {
        var risultati = Calcola(10, 3);
        Console.WriteLine($"Somma: {risultati.Somma}, Differenza: {risultati.Differenza}");
    }
}
```
### Vantaggi delle tuple

-  Leggere e veloci da creare e usare.
- Riduzione del boilerplate: niente classi aggiuntive per dati temporanei.
- Supportano nomi chiari per una migliore leggibilità.
- Perfette per funzioni che restituiscono più valori.

### Differenza tra classi, tuple e struct

| Caratteristica       | Class                  | Struct              | Tuple                  |
|---------------------|----------------------|-------------------|----------------------|
| Tipo                 | Reference Type        | Value Type        | Value / Reference    |
| Memoria              | Heap                  | Stack             | Stack / Heap         |
| Ereditarietà         | Sì                    | No (solo interfacce) | No                   |
| Mutabilità           | Tipicamente mutabile  | Consigliato immutabile | Immutabile          |
| Copia                | Variabili contengono riferimenti allo stesso oggetto | Copia dei valori → variabili indipendenti | Copia immutabile per default |
| Uso tipico           | Oggetti complessi con logica e stato | Dati piccoli e immutabili (coordinate, date) | Ritorno multiplo valori da metodi |
| Esempio             | `class Persona { public string Nome { get; set; } }` | `struct Point { public int X, Y; }` | `(string Nome, int Eta) persona = ("Luca", 25);` |

### Sintesi:

>Una tuple è un contenitore leggero che raggruppa più valori insieme.
>Può essere nominata o non nominata e permette di restituire più valori da un metodo senza creare una classe dedicata.
>È **immutabile** e molto utile per semplificare il **codice temporaneo** o di **supporto**.

## Cos'è un operatore ternario?`?:`

L’operatore ternario è un **modo compatto di scrivere un `if/else`**.  

Sintassi:
```csharp
condizione ? valoreSeVero : valoreSeFalso;
```

 Esempio:

```csharp
int eta = 20; string messaggio = eta >= 18 ? "Maggiorenne" : "Minorenne"; Console.WriteLine(messaggio); // Maggiorenne
```
### Con più condizioni (annidamento)

```csharp
int voto = 85; string risultato = voto >= 90 ? "Eccellente" :                     
                                  voto >= 70 ? "Buono" :                     
                                  "Insufficiente";  

Console.WriteLine(risultato); // Buono
```

### Vantaggi:

- Codice più compatto e leggibile per condizioni semplici.
- Perfetto per assegnazioni rapide o valori di ritorno da metodi.
  
## Parole chiave `in`, `ref` e `out` in Csharp

In C#, queste parole chiave controllano **come i parametri vengono passati ai metodi**. Sono molto importanti per capire **referenze, performance e comportamento dei valori**.

---
### 1. `ref` (Reference)

- Passa il **parametro per riferimento**.  
- Il metodo può **modificare il valore della variabile originale**.  
  
- ==La variabile deve essere **inizializzata prima** di essere passata.==  

```csharp
void Incrementa(ref int numero)
{
    numero += 1;
}

int a = 5;
Incrementa(ref a);
Console.WriteLine(a); // 6 → il valore originale è cambiato
```
### 2. `out` (Output)

- Passa il **parametro per riferimento** ma ==senza richiedere inizializzazione==.
- Il metodo deve assegnare un valore prima di terminare.

> Utile quando si vuole ritornare più valori senza usare tuple o classi.

```csharp

void Calcola(int a, int b, out int somma, out int differenza)
{
    somma = a + b;
    differenza = a - b;
}

int s, d;
Calcola(10, 3, out s, out d);
Console.WriteLine($"Somma={s}, Differenza={d}"); // Somma=13, Differenza=7
```
### 3. `in` (Input readonly reference)

- Passa il **parametro per riferimento** ma ==solo in lettura.==
- Migliora le prestazioni per tipi grandi (es. struct grandi), evitando copie.
- Non permette di modificare la variabile dentro il metodo.

```csharp
struct Point
{
    public int X, Y;
}

void Stampa(in Point p)
{
    Console.WriteLine($"X={p.X}, Y={p.Y}");
    // p.X = 10; // Errore! in è readonly
}

Point pt = new Point { X = 5, Y = 10 };
Stampa(in pt);
```
### 4. Sintesi veloce
|Parola chiave|Passaggio|Modifica permessa?|Variabile inizializzata?|
|---|---|---|---|
|`ref`|riferimento|Sì|Sì|
|`out`|riferimento|Sì|No (deve essere assegnata dal metodo)|
|`in`|riferimento readonly|No|Sì|

### 5. Quando usarli
- `ref`: quando vuoi modificare il valore originale e deve essere già inizializzato.
- `out`: quando vuoi **ritornare più valori** senza inizializzazione preventiva.
- `in`: per passare **grandi struct o dati** evitando copie, senza modificarli.

### Sintesi:

>`ref` permette di passare una variabile per riferimento e modificarla.  
>`out` permette di passare una variabile non inizializzata che il metodo riempirà.  
>`in` permette di passare una variabile per riferimento **solo in lettura**, utile per performance.
##  Classi Astratte in Csharp

Una **classe astratta** in C# è una classe che **non può essere istanziata direttamente** e serve come **modello per classi derivate**.  
Permette di definire **metodi e proprietà comuni** e lasciare **alcuni metodi senza implementazione**, che devono essere implementati nelle classi figlie.

---
### 1. Caratteristiche principali

- Non può essere **istanziata** direttamente.  

- Può contenere:
  - **Metodi concreti** (con implementazione)  
  - **Metodi astratti** (senza implementazione, solo firma)  
  - **Proprietà, campi e costruttori**  
- Serve a **forzare le classi derivate** a implementare determinati comportamenti.  
- Supporta **ereditarietà**, come le normali classi.

---
##### Sintassi ed esempio

```csharp
// Classe astratta
public abstract class Animale
{
    public string Nome { get; set; }

    // Metodo concreto
    public void Dormi()
    {
        Console.WriteLine($"{Nome} sta dormendo.");
    }

    // Metodo astratto (deve essere implementato nelle classi derivate)
    public abstract void Verso();
}

// Classe derivata concreta
public class Cane : Animale
{
    public override void Verso()
    {
        Console.WriteLine("Bau Bau!");
    }
}

class Program
{
    static void Main()
    {
        // Animale a = new Animale(); // Errore! Non si può istanziare
        Animale c = new Cane { Nome = "Fido" };
        c.Dormi();  // Fido sta dormendo.
        c.Verso();  // Bau Bau!
    }
}
```

### Differenze tra Classe Astratta e Interfaccia

|Caratteristica|Classe Astratta|Interfaccia|
|---|---|---|
|Istanza|No|No|
|Metodi concreti|Sì|No (tranne default interface methods da C# 8.0)|
|Ereditarietà|Singola + derivata|Multipla (può implementare più interfacce)|
|Campi e proprietà|Sì|Solo proprietà e metodi, senza campi|
|Costruttore|Sì|No|
### Sintesi: 
>Una **classe astratta** fornisce un modello comune con metodi concreti e astratti, obbliga le classi derivate a implementare determinati comportamenti, e non può essere istanziata direttamente.  
>È utile quando vuoi **condividere codice comune** e **definire un contratto obbligatorio** per le classi figlie.

## Interfacce in Csharp

Un’**interfaccia** (`interface`) è un insieme di regole e protocolli che definisce un **contratto** che più classi o struct devono rispettare, consentendogli di essere trattate in modo uniforme attraverso l'interfaccia comune.
Stabilisce **cosa un oggetto deve fare**, ma ==non come farlo== (tranne i metodi di default introdotti in C# 8.0).

>Sono spesso utilizzate per definire contratti tra **componenti software**, facilitando l'integrazione e la sostituzione di componenti ==senza modificare il codice che li utilizza==.

---

### 1. Caratteristiche principali

- Un'interfaccia può contenere solo **dichiarazioni di membri** (metodi, proprietà, eventi, indici) senza implementazioni concrete.
  Non può avere quindi ne' campi o costruttori.
- Tutti i membri sono **implicitamente pubblici e astratti** (non si scrive `abstract` o `public`).  
- Permette di ottenere **polimorfismo** e design flessibili.  
- Una classe può implementare **più interfacce** (ereditarietà multipla).  
- Le interfacce possono avere **metodi di default** (C# 8.0+) con implementazione. 

- Le interfacce possono essere utilizzate per definire **callback** o **eventi**, consentendo a un oggetto di ==notificare altri oggetti quando si verificano determinati eventi==.
-  Le interfacce possono essere utilizzate per definire **API pubbliche**, consentendo a diverse parti di un'applicazione di comunicare tra loro in modo standardizzato.
- Le interfacce possono essere utilizzate per definire **comportamenti comuni tra classi non correlate**, facilitando la **coesione del codice** e la manutenzione.


---
<span style="color: #e5a216">Sintassi ed esempio:</span>

```csharp

// Definizione di un'interfaccia
public interface IVeicolo
{
    void Muovi();
    int VelocitaMassima { get; }
}

// Implementazione in una classe
public class Auto : IVeicolo
{
    public int VelocitaMassima { get; private set; } = 200;

    public void Muovi()
    {
        Console.WriteLine("L'auto sta guidando!");
    }
}

// Implementazione in un'altra classe
public class Barca : IVeicolo
{
    public int VelocitaMassima { get; private set; } = 80;

    public void Muovi()
    {
        Console.WriteLine("La barca sta navigando!");
    }
}

class Program
{
    static void Main()
    {
        IVeicolo v1 = new Auto();
        IVeicolo v2 = new Barca();

        v1.Muovi(); // L'auto sta guidando!
        v2.Muovi(); // La barca sta navigando!
    }
}
```

### Differenze tra Interfaccia e Classe Astratta

| Caratteristica  | Interfaccia                  | Classe Astratta              |
| --------------- | ---------------------------- | ---------------------------- |
| Istanza         | No                           | No                           |
| Metodi concreti | Solo default (C#8+)          | Sì                           |
| Ereditarietà    | Multipla                     | Singola                      |
| Campi           | No                           | Sì                           |
| Costruttore     | No                           | Sì                           |
| Scopo           | Contratto puramente astratto | Contratto + codice condiviso |
### Sintesi: 

> Le **interfacce** definiscono un contratto che le classi devono rispettare.  
> Consentono il **polimorfismo** e l’**ereditarietà multipla** dei comportamenti,  
> e sono ideali per progettare codice **flessibile, modulare e facilmente testabile**.


## Cos’è una **nested class**?

Una **nested class** è una classe **definita all’interno di un’altra classe**.
Viene utilizzata quando una classe è **strettamente legata alla classe esterna** e non ha senso esistere separatamente.

- Serve a raggruppare classi che hanno **forte relazione logica**.
- Può ==accedere ai membri privati della classe esterna== se non è `static`.
- Le classi annidate possono essere **static** o **non static**.
  
### Caratteristiche principali

- Può essere **privata, protetta, pubblica o interna**, a seconda di quanto deve essere visibile fuori dalla classe esterna.  
- Ha accesso **ai membri privati** della classe esterna.  
- Utile per **organizzare il codice** e raggruppare funzionalità correlate.  
- Supporta l’**incapsulamento**: puoi nascondere dettagli interni alla classe esterna.


<span style="color: #e5a216">Sintassi base</span>

```csharp
class Esterno {
    private int numero = 10;

    // Classe annidata
    public class Interno 
    {
        public void Saluta() 
        {
            Console.WriteLine("Ciao dalla classe annidata!");
        }
    }
}

class Program 
{
    static void Main() 
    {
        Esterno.Interno obj = new Esterno.Interno();
        obj.Saluta(); // Output: Ciao dalla classe annidata!
    }
}
```

---

#### Nested class **non static** che accede ai membri esterni

```csharp
class Esterno {
    private int numero = 42;

    public class Interno 
    {
        private Esterno esterno;

        public Interno(Esterno e) 
        {
            esterno = e;
        }

        public void MostraNumero() 
        {
            Console.WriteLine($"Numero dalla classe esterna: {esterno.numero}");
        }
    }
}

class Program {
    static void Main() 
    {
        Esterno est = new Esterno();
        Esterno.Interno interno = new Esterno.Interno(est);
        interno.MostraNumero(); // Numero dalla classe esterna: 42
    }
}

```

`-->` Le classi annidate sono utili per **organizzare meglio il codice** e creare classi di supporto che **non hanno senso fuori dal contesto** della classe esterna.

---

### Quando usare le nested class

1. Classi di supporto strettamente legate a una classe principale.
2. Incapsulare dettagli **interni** senza esporli all’esterno.
3. Migliorare **leggibilità** e **manutenibilità** del codice.

### Sintesi:

> Le **interfacce** definiscono un contratto che le classi devono rispettare.  
> Consentono il **polimorfismo** e l’**ereditarietà multipla** dei comportamenti,  
> e sono ideali per progettare codice **flessibile, modulare e facilmente testabile**.

## Partial Class in Csharp

Una **partial class** in C# permette di **dividere la definizione di una classe in più file**.  

---
### 1. Caratteristiche principali

- Tutte le parti devono avere lo **stesso namespace e lo stesso nome di classe**.  
- Tutte le parti devono avere lo stesso **modificatore di accesso** (`public`, `internal`, ecc.).  
- Durante la compilazione, il compilatore **unisce tutte le parti in un’unica classe**.  
- Utile in **progetti grandi** o quando **parte del codice è generata automaticamente** (es. designer di Windows Forms, ASP.NET).  

---
### 2. Esempio pratico

**File 1: Persona.Part1.cs**

```csharp
public partial class Persona
{
    public string Nome { get; set; }
    public string Cognome { get; set; }

    public void Saluta()
    {
        Console.WriteLine($"Ciao, sono {Nome} {Cognome}");
    }
}
```

**File 2: Persona.Part2.cs**

```csharp
public partial class Persona
{
    public int Eta { get; set; }

    public void MostraEta()
    {
        Console.WriteLine($"Ho {Eta} anni");
    }
}
```

Uso della classe:

```csharp
class Program
{
    static void Main()
    {
        Persona p = new Persona { Nome = "Luca", Cognome = "Rossi", Eta = 25 };
        p.Saluta();       // Ciao, sono Luca Rossi
        p.MostraEta();    // Ho 25 anni
    }
}
```
### 3. Vantaggi
- Mantiene il codice **organizzato** in più file.
- Facilita la collaborazione tra più sviluppatori sullo stesso progetto.
- Permette di ==estendere classi generate automaticamente== senza modificare il file originale.

### 4. Sintesi
>Una partial class consente di dividere una classe in più file, tutte le parti vengono unite dal compilatore in fase di build.
>Utile per organizzazione, collaborazione e integrazione con codice generato automaticamente.

## Classi e membri **Static**

La parola chiave **`static`** in C# indica che un membro o una classe **appartiene al tipo stesso e non a un’istanza**.  

- **Campo static** → condiviso da tutte le istanze.
- **Metodo static** → può essere chiamato senza creare un oggetto.
- **Classe static** → può contenere solo membri statici (in C#).

Quindi ==non è necessario creare un oggetto per usarlo==.

---
### Caratteristiche principali

- Una **classe statica**:

  - Non può essere istanziata (`new` non è permesso).
  - Contiene solo **membri statici** (metodi, proprietà, campi, costanti).
  - Viene caricata una sola volta in memoria durante l’esecuzione.

- Un **metodo/proprietà/variabile static**:

  - Appartiene al tipo, non alla singola istanza.
  - Può essere richiamato direttamente tramite il nome della classe.

---

#### Esempio di classe statica

```csharp
public static class MathHelper
{
    public static double Pi = 3.14159;

    public static double Somma(double a, double b)
    {
        return a + b;
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine(MathHelper.Pi);            // 3.14159
        Console.WriteLine(MathHelper.Somma(3, 5));   // 8
    }
}
```

👉 Non serve istanziare MathHelper.

#### Esempio con membri statici in una classe normale
```csharp
public class Contatore
{
    private static int _totale = 0; // condiviso tra tutte le istanze

    public Contatore()
    {
        _totale++;
    }

    public static int TotaleIstanze => _totale;
}

class Program
{
    static void Main()
    {
        new Contatore();
        new Contatore();
        Console.WriteLine(Contatore.TotaleIstanze); // 2
    }
}
```

👉 La variabile static <span style="color: #2d98da">totale</span> è condivisa da tutte le istanze.

### Differenze chiave
| Caratteristica    | Static                   | Non Static (istanza)       |
| ----------------- | ------------------------ | -------------------------- |
| Appartenenza      | Alla classe              | All’oggetto istanziato     |
| Memoria           | Una sola copia condivisa | Una copia per ogni istanza |
| Creazione oggetto | Non serve `new`          | Necessario `new`           |
| Override/virtual  | Non supportato           | Supportato                 |
>`static` indica che un membro appartiene al tipo stesso e non all’istanza.  
>Una **classe statica** non può essere istanziata e serve per metodi e costanti condivise.  
> I **membri statici** invece sono condivisi tra tutte le istanze della classe.

## La parola chiave **this** in Csharp

In C#, **`this`** è un riferimento all'**istanza corrente della classe**.  
Si usa per accedere a membri, proprietà e metodi dell’oggetto dentro la sua definizione.

Serve per:

- Distinguere tra campi e parametri con lo stesso nome.
- Passare l’oggetto corrente come argomento.
- Accedere a membri della stessa classe.
    
### Caratteristiche principali

- Riferisce all’**oggetto corrente** ==(l’istanza su cui è stato invocato un metodo o costruttore==).  
- Risolve **ambiguità tra variabili locali e campi della classe**.  
- Può essere usato per **chiamare altri costruttori** nella stessa classe.  
- In metodi di **estensione**, rappresenta l’oggetto su cui il metodo è invocato.  

### Esempio di utilizzo in C#

#### Disambiguazione
```csharp
public class Persona
{
    private string nome;

    public Persona(string nome)
    {
        // Senza "this", il parametro e il campo hanno lo stesso nome
        this.nome = nome;
    }

    public void Saluta()
    {
        Console.WriteLine($"Ciao, sono {this.nome}");
    }
}

class Program
{
    static void Main()
    {
        Persona p = new Persona("Luca");
        p.Saluta(); // Ciao, sono Luca
    }
}
```

👉 <span style="color: #8392a4">this.nome</span> indica che si tratta del campo, non del parametro.

#### Chiamare un altro costruttore
```csharp
public class Rettangolo
{
    public int Larghezza { get; }
    public int Altezza { get; }

    public Rettangolo(int lato) : this(lato, lato) { } // usa altro costruttore

    public Rettangolo(int larghezza, int altezza)
    {
        this.Larghezza = larghezza;
        this.Altezza = altezza;
    }
}
```

👉 `: this(...)` permette di riutilizzare la logica di un costruttore.

#### Nei metodi di estensione
```csharp
public static class Estensioni
{
    public static int ContaCaratteri(this string testo)
    {
        return testo.Length;
    }
}

class Program
{
    static void Main()
    {
        string s = "Ciao";
        Console.WriteLine(s.ContaCaratteri()); // 4
    }
}

```
👉 Qui `this string testo` indica che il metodo è un’estensione di `string`.

#### Sintesi:

> `this` è un riferimento all’istanza corrente della classe.  
> Serve per accedere ai membri dell’oggetto, risolvere ambiguità,  
> richiamare costruttori interni o definire metodi di estensione.

## **Base** in Csharp

In C#, le parole chiave **`this`** e **`base`** servono entrambe per riferirsi all’oggetto corrente,  ma hanno scopi diversi in relazione all’ereditarietà.

`base`:

- Si riferisce alla **classe base (superclasse)** dell’oggetto corrente.
- Permette di accedere a **metodi, proprietà e campi ereditati** dalla classe madre.
- Usato per richiamare **costruttori della classe base** (`: base(...)`).
- Utile quando un membro è stato **sovrascritto** con `override` e vogliamo invocare anche la versione della superclasse.
    

**Esempio:**
```csharp
public class Animale
{
    public virtual void Verso()
    {
        Console.WriteLine("Verso generico");
    }
}

public class Cane : Animale
{
    public override void Verso()
    {
        base.Verso(); // chiama il metodo della classe base
        Console.WriteLine("Bau Bau!");
    }
}

```
---

### Costruttori con `base`
```csharp
public class Persona
{
    public string Nome { get; }

    public Persona(string nome)
    {
        Nome = nome;
    }
}

public class Studente : Persona
{
    public string Matricola { get; }

    public Studente(string nome, string matricola) : base(nome) // chiama costruttore della base
    {
        Matricola = matricola;
    }
}
```

### Tabella comparativa

| Parola chiave | Riferimento a...     | Uso principale                                                                |
| ------------- | -------------------- | ----------------------------------------------------------------------------- |
| `this`        | Istanza corrente     | Accedere ai membri dell’oggetto, disambiguare, richiamare costruttori interni |
| `base`        | Classe madre (super) | Richiamare membri della classe base, invocare costruttori della superclasse   |
### Sintesi

> `this` si riferisce all’istanza corrente della classe,  
> mentre `base` permette di accedere ai membri della classe madre.  
> Si usa `this` per riferirsi ai membri della stessa classe e `base` per estendere o riutilizzare comportamenti della superclasse

## Gestione delle Eccezioni in C#

In C# gli errori a runtime vengono gestiti tramite **eccezioni**.  

Il blocco tipico è:

```csharp
try
{
    // Codice che può generare eccezioni
}
catch (TipoEccezione ex)
{
    // Gestione specifica dell’eccezione
}
finally
{
    // Codice che viene SEMPRE eseguito (anche in caso di errore)
}

```
---
### Struttura

1. **`try`**
    
    - Contiene il codice “a rischio” di errori.
    - Se avviene un’eccezione, l’esecuzione si interrompe lì e passa al `catch`.
        
2. **`catch`**
    
    - Intercetta un’eccezione di un certo tipo (`Exception`, `IOException`, `DivideByZeroException`, ecc.).
    - Può esserci più di un `catch`, dall’eccezione più specifica alla più generica.
        
3. **`finally`**
    
    - Viene sempre eseguito, indipendentemente da successi o errori.
    - Utile per **rilasciare risorse** (connessioni DB, file, ecc.).

---

### Esempio base

```csharp
try
{
    int a = 10, b = 0;
    int result = a / b; // DivideByZeroException
}
catch (DivideByZeroException ex)
{
    Console.WriteLine("Errore: divisione per zero!");
}
catch (Exception ex)
{
    Console.WriteLine($"Altro errore: {ex.Message}");
}
finally
{
    Console.WriteLine("Blocco finally eseguito.");
}
```

 Output:
```diff 
Errore: divisione per zero!
Blocco finally eseguito.`
```
---
### Best practice

- **Catturare prima le eccezioni specifiche**, poi quelle generiche.
- Usare `finally` o `using` per rilasciare risorse.
- Non abusare delle eccezioni per logica di controllo → sono costose in termini di performance.
  
## Generics in Csharp

I **Generics** permettono di scrivere classi, metodi, interfacce e strutture **indipendenti dal tipo**, garantendo:
- **Riutilizzo** → stesso codice con tipi diversi.
- **Sicurezza** → controlli a compile-time, niente cast non sicuri.
- **Performance** → niente boxing/unboxing per tipi valore.

---
### Esempio semplice: classe generica
```csharp
// Classe "scatola" che può contenere qualsiasi tipo
public class Box<T>
{
    public T Contenuto { get; set; }

    public Box(T valore)
    {
        Contenuto = valore;
    }
}

// Uso
var boxInt = new Box<int>(42);
var boxString = new Box<string>("Ciao");

Console.WriteLine(boxInt.Contenuto);   // 42
Console.WriteLine(boxString.Contenuto); // Ciao

```

👉 `T` è un **parametro di tipo**. Può essere chiamato come vuoi (`TKey`, `TValue`, ecc.), ma `T` è convenzione.

---
### Metodo generico

```csharp
public class Utility
{
    public static void Stampa<T>(T valore)
    {
        Console.WriteLine($"Valore: {valore}");
    }
}

// Uso
Utility.Stampa(123);       // Valore: 123
Utility.Stampa("Hello");   // Valore: Hello

``` 
---
### Interfacce generiche

```csharp
public interface IRepository<T>
{
    void Add(T item);
    T Get(int id);
}
```

---

### 🔹 Vincoli (`where`)

Per limitare il tipo usato nei generics:

```csharp
public class Repository<T> where T : class
{
    public void Save(T entity) { /*...*/ }
}
```
Possibili vincoli:

- `where T : class` → solo reference type.
- `where T : struct` → solo value type.
- `where T : new()` → deve avere costruttore vuoto.
- `where T : BaseClass` → deve derivare da `BaseClass`.
- `where T : IInterface` → deve implementare interfaccia.

---

### Esempio con vincoli multipli
```csharp
public class Service<T> where T : BaseEntity, new()
{
    public T Create()
    {
        return new T(); // ok grazie a new()
    }
}
```

---

### 🔹 Collezioni generiche in .NET

- `List<T>` → lista dinamica.
- `Dictionary<TKey, TValue>` → mappa chiave/valore.
- `Queue<T>`, `Stack<T>` → strutture dati tipizzate.
---

👉 I **Generics** sono alla base di LINQ, Entity Framework, Repository pattern, collezioni moderne, ecc.

## String vs StringBuilder in Csharp

In C# sia `string` che `StringBuilder` gestiscono testo, ma hanno differenze fondamentali legate a **immutabilità** e **performance**.

---
### <span style="color: #8392a4">string</span>

- **Tipo**: Reference type (`System.String`), ==immutabile.==
- **Immutabilità**: una volta creata, non può essere modificata → ogni concatenazione crea un nuovo oggetto.
- **Uso tipico**: testi piccoli, concatenazioni sporadiche.
- **Performance**: poco efficiente in cicli con molte concatenazioni.
    
```csharp
`string s = "Hello"; s += " World"; // crea un NUOVO oggetto string Console.WriteLine(s); // "Hello World"`
```

⚠️ Ogni `+=` crea una nuova stringa → consumo di memoria maggiore.

---

### <span style="color: #8392a4">StringBuilder</span>

- **Tipo**: Classe `System.Text.StringBuilder`.
- **Mutabilità**: ==modificabile senza creare nuovi oggetti==.
- **Uso tipico**: concatenazioni ripetute, costruzione di stringhe complesse.
- **Performance**: molto più efficiente in loop o grosse manipolazioni di testo.
    
```csharp
using System.Text;  

StringBuilder sb = new StringBuilder("Hello"); sb.Append(" World"); sb.Append("!"); 

Console.WriteLine(sb.ToString()); // "Hello World!"
```

👉 Qui lo stesso oggetto `sb` viene modificato, senza ricreare nuove stringhe.

---

### Differenze chiave

|Caratteristica|`string`|`StringBuilder`|
|---|---|---|
|Tipo|Reference|Reference|
|Mutabilità|❌ Immutabile|✅ Mutabile|
|Performance concatenazioni|❌ Scarsa (ricrea ogni volta)|✅ Ottima|
|Uso consigliato|Testi piccoli, letture|Testi grandi, concatenazioni ripetute|
|Namespace|`System`|`System.Text`|

---

### Regola pratica

- Usa `string` → quando hai poche concatenazioni e testo semplice.
- Usa `StringBuilder` → quando costruisci stringhe in loop o testi lunghi (es. generazione di report, JSON, HTML).
  
## Preprocessore in Csharp

In C# non c’è un vero preprocessore come in C/C++ (che “taglia e incolla” codice), ma ci sono **direttive di preprocessore** che danno istruzioni al compilatore.  
Non generano codice ma influenzano **come viene compilato**.

### Principali direttive

- `#define` / `#undef` → definisce o rimuove un simbolo.
- `#if`, `#elif`, `#else`, `#endif` → compilazione condizionale.
- `#region` / `#endregion` → raggruppa codice piegabile in editor.
- `#warning` / `#error` → mostra avvisi o errori custom in compilazione.
- `#pragma` → disabilita/abilita warning specifici.
- `#line` → modifica il numero di riga o il file usato dal compilatore per messaggi di errore.
### Esempio pratico (compilazione condizionale)

```csharp
#define DEBUG  

using System;

class Program
{
    static void Main()
    {
#if DEBUG
        Console.WriteLine("Compilazione in modalità DEBUG");
#else
        Console.WriteLine("Compilazione in modalità RELEASE");
#endif
    }
}
```

👉 Questo permette di includere o escludere blocchi di codice in base alla build.

---
## Garbage Collector (GC) in .NET

Il **GC (Garbage Collector)** è il sistema automatico di **gestione della memoria** in .NET.  
Si occupa di:

1. Allocare memoria per gli oggetti sul **heap**.
2. Rimuovere gli oggetti non più utilizzati (non raggiungibili).
3. Liberare memoria senza bisogno di `free()` o `delete` (come in C++).

---
### Come funziona

- Ogni oggetto creato con `new` va nello **managed heap**.
- Quando non ci sono più riferimenti → diventa **eligible for collection**.
- Il GC gira periodicamente e rilascia memoria.
- Gestisce anche **compattazione** → elimina frammentazione.

---
### Generazioni del GC

Per ottimizzare performance, .NET divide gli oggetti in **generazioni**:

- **Gen 0** → oggetti temporanei (es. variabili locali). Raccolta frequente.
- **Gen 1** → oggetti “giovani” sopravvissuti a Gen 0.
- **Gen 2** → oggetti a lunga durata (es. statici, globali). Raccolta meno frequente.

👉 Idea: gli oggetti che sopravvivono a più cicli probabilmente vivranno a lungo, quindi non ha senso ricontrollarli continuamente.

---

### Forzare la Garbage Collection

In casi rari puoi forzare il GC manualmente:

```csharp

GC.Collect(); 
GC.WaitForPendingFinalizers();

```

⚠️ **Sconsigliato** in produzione → meglio lasciare la gestione al runtime.

---
### Finalizer e IDisposable

- Il **finalizer (`~ClassName()`)** viene chiamato dal GC prima di liberare memoria, ma non si sa _quando_.
- Per liberare risorse non gestite (es. file, connessioni DB) si usa **`IDisposable` + `using`**:
    

```csharp
using (var file = new StreamWriter("log.txt"))
{
    file.WriteLine("Hello GC!");
} // qui viene chiamato Dispose() automaticamente

```
---
### Differenza chiave

- **Preprocessore** → influenza _come_ il codice viene compilato.
- **GC** → influenza _come_ il codice viene eseguito e gestisce memoria automaticamente.

## `sealed` in C

Il modificatore **`sealed`** serve per **bloccare l’ereditarietà o l’override**.  
Può essere applicato sia a **classi** che a **metodi**.

---

### 1. `sealed` su classi

Una **classe sealed** non può essere ereditata.  

👉 Utile quando vuoi impedire estensioni non desiderate.

```csharp
public sealed class Logger
{
    public void Log(string msg) => Console.WriteLine(msg);
}

// ❌ Errore: non posso ereditare da sealed
// public class MyLogger : Logger { }
```

---

### 2. `sealed` su metodi

Un **metodo sealed** può essere usato solo su un metodo **virtual** ereditato e impedisce ulteriori override nelle classi derivate.

```csharp
public class Base
{
    public virtual void Show() => Console.WriteLine("Base");
}

public class Derived : Base
{
    public sealed override void Show() => Console.WriteLine("Derived");
}

public class MoreDerived : Derived
{
    // ❌ Errore: Show è sealed, non posso fare override
    // public override void Show() { }
}
```
---

### Quando usarlo?

- **Performance** → il compilatore può ottimizzare meglio le chiamate.
- **Design** → garantisci che certe classi/metodi non vengano estesi o modificati.
- **Sicurezza** → eviti modifiche indesiderate ad API pubbliche.
    

---

### Confronto rapido

|Modificatore|Significato|
|---|---|
|`abstract`|Deve essere ereditata/implementata|
|`virtual`|Può essere ridefinita nelle derivate|
|`override`|Ridefinisce un metodo virtual/astratto|
|`sealed`|Blocca ereditarietà/override|

## Delegate, Eventi e Lambda in Csharp

---
### 1. Delegate

Un **delegate** è un **tipo che rappresenta un riferimento a un metodo** (simile a un “puntatore a funzione” ma type-safe).

👉 Ti permette di passare metodi come parametri, memorizzarli in variabili e invocarli.

```csharp
// Dichiarazione delegate
public delegate void Notifica(string msg);

class Program
{
    static void Main()
    {
        // Associo metodo al delegate
        Notifica handler = StampaMessaggio;
        handler("Ciao dal delegate!"); // Invocazione
    }

    static void StampaMessaggio(string testo)
    {
        Console.WriteLine(testo);
    }
}
```

> Delegate = “contratto” su **cosa** un metodo deve **accettare e restituire**.

---
### 2. Eventi

Un **evento** è basato su un delegate ma usato per il **pattern Publisher/Subscriber**.

- Una classe **publisher** dichiara un evento.
- Altri oggetti possono **iscriversi** (subscribe) o **disiscriversi** (unsubscribe).
- Solo il publisher può **scatenare** l’evento.

```csharp
// Dichiarazione delegate
public delegate void Notifica(string msg);

class Program
{
    static void Main()
    {
        // Associo metodo al delegate
        Notifica handler = StampaMessaggio;
        handler("Ciao dal delegate!"); // Invocazione
    }

    static void StampaMessaggio(string testo)
    {
        Console.WriteLine(testo);
    }
}
```

> Gli eventi sono fondamentali in GUI, UI, programmazione asincrona.

---
### 3. Lambda Expression

Una **lambda expression** è una **funzione anonima** (senza nome) scritta in forma compatta.  

Sintassi:

```csharp
(parametri) = > espressione
```

Esempio:

```csharp
Func<int, int, int> somma = (a, b) => a + b;
Console.WriteLine(somma(3, 4)); // 7

Action<string> stampa = s => Console.WriteLine(s);
stampa("Hello Lambda!");

```

- `Func<T,...>` → ritorna un valore.
- `Action<T,...>` → non ritorna nulla.
- `Predicate<T>` → ritorna `bool`.

---
### Collegamento tra i tre

- **Delegate** → il concetto base (tipo che rappresenta un metodo).
- **Eventi** → usano delegate per notificare azioni a più sottoscrittori.
- **Lambda** → modo rapido di scrivere funzioni inline da assegnare a delegate o eventi.
---

### Mini tabella riassuntiva

|Concetto|Cos’è|Esempio tipico|
|---|---|---|
|**Delegate**|Riferimento a un metodo|`delegate void Notifica(string msg)`|
|**Evento**|Delegate con Publisher/Subscriber|`button.OnClick += Handler;`|
|**Lambda**|Funzione anonima compatta|`(x,y) => x+y`|
## `async` e `await` in Csharp

- In .NET il modello **asincrono** serve a non bloccare il thread principale (UI o richieste web).

- `async` → usato per marcare un metodo come asincrono.
- `await` → sospende l’esecuzione fino al completamento di un’operazione asincrona, **senza bloccare il thread**.

👉 Dietro le quinte si basano su `Task` e `Task<T>`.

---

### Metodo sincrono (bloccante)

```csharp
public string ScaricaDati()
{
    Thread.Sleep(3000); // Simula operazione lenta
    return "Dati scaricati!";
}
```

⚠️ Questo **blocca il thread** per 3 secondi.

---
### Metodo asincrono con `async` / `await`

```csharp
public async Task<string> ScaricaDatiAsync()
{
    await Task.Delay(3000); // Simula attesa non bloccante
    return "Dati scaricati!";
}

public async Task Main()
{
    Console.WriteLine("Download in corso...");
    string risultato = await ScaricaDatiAsync();
    Console.WriteLine(risultato);
}
```

👉 `await Task.Delay(3000)` non blocca il thread → il programma può fare altre cose in attesa.

---
### Tipi di ritorno ammessi

- `Task` → metodo asincrono che non ritorna nulla.
- `Task<T>` → metodo asincrono che ritorna un valore di tipo `T`.
- `void` → solo per gestori di eventi asincroni (es. `button.Click`).

---
### Vantaggi

- Evita UI congelata nelle app desktop/mobile.
- Migliora la scalabilità nelle web app (ASP.NET).
- Codice più leggibile rispetto a callback o `ContinueWith`.

---
### Esempio reale (chiamata HTTP asincrona)

```csharp
using System.Net.Http;

public async Task<string> GetGooglePageAsync()
{
    using var client = new HttpClient();
    string html = await client.GetStringAsync("https://www.google.com");
    return html;
}
```

✅ Qui l’app non resta bloccata in attesa della risposta del server.

---
### Tabella riassuntiva

| Parola chiave  | Significato                                                                       |
| -------------- | --------------------------------------------------------------------------------- |
| **async**      | Marca un metodo come asincrono e permette l’uso di `await`.                       |
| **await**      | Sospende l’esecuzione fino al completamento del `Task`, senza bloccare il thread. |
| **Task**       | Rappresenta un’operazione asincrona.                                              |
| **Task<T<T>>** | Operazione asincrona che ritorna un risultato.                                    |
### Differenze chiave tra sincrono e asincrono?

In C# e in .NET ci sono casi in cui **i metodi sincroni sono perfettamente appropriati**.

|Caratteristica|Sincrono|Asincrono (`async/await`)|
|---|---|---|
|Blocco del thread|✅ Sì, il thread resta fermo|❌ No, il thread può continuare|
|Complessità codice|✅ Semplice|⚠ Più complesso (Task, await)|
|Uso tipico|Operazioni veloci, CPU-bound, calcoli interni|I/O-bound (file, DB, rete, API)|
|Performance in UI / server|❌ UI può congelarsi / thread occupato|✅ UI fluida / più richiesto per server scalabile|

---

### Quando usare metodi sincroni

1. **Operazioni veloci** che terminano subito (calcoli, elaborazioni in memoria).
2. **Codice che non coinvolge I/O** (non accede a file, DB o rete).
3. **Semplicità** → per utility, helper, piccoli algoritmi.

```csharp
public int Somma(int a, int b) 
{     
return a + b; 
}
```

4. **Compatibilità** → alcune API legacy richiedono ancora metodi sincroni.

---

### Quando preferire metodi asincroni

1. Accesso a **file, DB, web service, API remote**.
2. Applicazioni **UI** → evitare freeze della UI.
3. Applicazioni **server** → aumentare throughput senza bloccare thread.
    

```csharp
public async Task<string> ScaricaFileAsync(string url)
{
    using var client = new HttpClient();
    return await client.GetStringAsync(url);
}

```

---

### Regola pratica

> Non tutto deve essere asincrono.  
> Usa metodi **sincroni** per operazioni veloci e CPU-bound,  usa **async/await** per I/O-bound o scenari dove bloccare il thread è problematico.

## Binding Statico vs Dinamico in Csharp

---

### 1. Binding Statico (Static Binding)

- Avviene a **compile-time**: il compilatore determina **quale metodo o proprietà chiamare** basandosi sul tipo dichiarato della variabile.  
- **Più efficiente** perché il compilatore può ottimizzare le chiamate.  
- **Meno flessibile**, perché il tipo deve essere noto a compile-time.  
- In C# è il comportamento predefinito.

### Esempio
```csharp
class Animale
{
    public void FaiVerso() => Console.WriteLine("Verso generico");
}

class Cane : Animale
{
    public void FaiVerso() => Console.WriteLine("Bau Bau");
}

class Program
{
    static void Main()
    {
        Animale a = new Cane(); // variabile di tipo Animale
        a.FaiVerso();           // Chiamata statica → stampa "Verso generico"
    }
}
```

Il compilatore decide il metodo da chiamare in base al tipo dichiarato (Animale), non al tipo reale (Cane).

### 2. Binding Dinamico (Dynamic Binding)

- Avviene a **runtime**: il metodo o la proprietà chiamata viene risolta in base al **tipo effettivo dell’oggetto**.
- Più flessibile, ideale per polimorfismo o tipi sconosciuti a compile-time.
- In C#, può essere ottenuto usando il tipo dynamic.

Esempio
```csharp
class Animale
{
    public virtual void FaiVerso() => Console.WriteLine("Verso generico");
}

class Cane : Animale
{
    public override void FaiVerso() => Console.WriteLine("Bau Bau");
}

class Program
{
    static void Main()
    {
        dynamic a = new Cane(); 
        a.FaiVerso(); // Risolto a runtime → stampa "Bau Bau"
    }
}
```
Qui il metodo corretto viene scelto in base al tipo effettivo (Cane) a runtime.

### 3. Confronto
|Caratteristica|Static Binding|Dynamic Binding|
|---|---|---|
|Quando avviene|Compile-time|Runtime|
|Tipo risolto|Tipo dichiarato|Tipo effettivo|
|Performance|Più veloce|Più lenta|
|Flessibilità|Limitata|Alta|
|Uso tipico|Tipi fortemente tipizzati|Polimorfismo, `dynamic`|

### 4️. Sintesi
>**Binding statico**: il compilatore sceglie il membro da chiamare in base al tipo dichiarato.
>**Binding dinamico**: la scelta avviene a runtime in base al tipo reale dell’oggetto, usando virtual, override o dynamic.


## Protocollo HTTP

- **HTTP** (Hypertext Transfer Protocol) è il protocollo di comunicazione tra **client** (es. browser, app) e **server** (web server, API).
    
- Funziona sul modello **request/response**:
    
    1. Il client invia una **richiesta HTTP** al server.
    2. Il server risponde con una **risposta HTTP**.
        
- È **stateless**, cioè ogni richiesta è indipendente dalle altre.

---

### 1. Richiesta HTTP (HTTP Request)

Una richiesta HTTP contiene:

|Componente|Descrizione|
|---|---|
|**Metodo**|Tipo di operazione (`GET`, `POST`, `PUT`, `DELETE`, ecc.)|
|**URL**|L’indirizzo della risorsa richiesta|
|**Headers**|Informazioni aggiuntive (es. `Content-Type`, `Authorization`)|
|**Body**|Contenuto inviato (solo per POST, PUT, PATCH)|

### Esempio di richiesta

```http
POST /api/clienti HTTP/1.1
Host: api.miosito.com
Content-Type: application/json

{
  "nome": "Luca",
  "eta": 30
}
```

---

### 2. Risposta HTTP (HTTP Response)

Una risposta HTTP contiene:

|Componente|Descrizione|
|---|---|
|**Status Code**|Codice numerico che indica l’esito della richiesta (200, 404, 500…)|
|**Status Message**|Messaggio testuale del codice (`OK`, `Not Found`)|
|**Headers**|Informazioni aggiuntive (es. `Content-Type`, `Set-Cookie`)|
|**Body**|Contenuto della risposta (es. JSON, HTML, testo)|

### Esempio di risposta

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "nome": "Luca",
  "eta": 30
}
```

---
### 3. Principali codici di stato HTTP

| Codice | Categoria     | Significato                                                              |
| ------ | ------------- | ------------------------------------------------------------------------ |
| 1xx    | Informativo   | Comunicazione iniziale                                                   |
| 2xx    | Successo      | Richiesta completata (200 OK, 201 Created)                               |
| 3xx    | Redirezione   | Risorsa spostata o redirect (301, 302)                                   |
| 4xx    | Errore client | Problema con la richiesta (400 Bad Request, 404 Not Found)               |
| 5xx    | Errore server | Problema sul server (500 Internal Server Error, 503 Service Unavailable) |

---

### 4. Metodi HTTP più comuni

|Metodo|Significato|
|---|---|
|GET|Richiede una risorsa senza modificarla|
|POST|Invia dati per creare una risorsa|
|PUT|Aggiorna completamente una risorsa|
|PATCH|Aggiorna parzialmente una risorsa|
|DELETE|Elimina una risorsa|

---

### 5. Sintesi
> - **HTTP** = protocollo client-server, request/response.
> - **Status code** indica l’esito: 2xx successo, 4xx errore client, 5xx errore server.
> - **Metodi HTTP** definiscono l’azione: GET, POST, PUT, PATCH, DELETE.
> - In .NET si interagisce con HTTP tramite **HttpClient**, Web API, MVC controller.

### <span style="color: #20bf6b">Evoluzione del protocollo HTTP</span>
---

#### HTTP/1.1 (1997 – ancora molto usato)

- **Connessioni persistenti (Keep-Alive)** → più richieste possono viaggiare sulla stessa connessione TCP (prima, con HTTP/1.0, ogni richiesta apriva una connessione nuova).
- **Pipelining** → possibilità di inviare più richieste senza aspettare la risposta. In teoria utile, ma mai adottato bene per problemi di compatibilità.
- **Chunked transfer encoding** → permette lo **streaming** di contenuti dinamici.
- **Nuovi metodi HTTP** → introdotti `PUT`, `DELETE`, `OPTIONS`.
- **Caching e header** migliorati rispetto a HTTP/1.0.
- **Limite principale**:
    
    - le risorse sono servite in sequenza → se una è lenta, blocca le altre (**Head of Line Blocking**).
    - i browser hanno aggirato il problema aprendo più connessioni TCP in parallelo (overhead elevato).
        

👉 Ancora oggi è il **fallback** se HTTP/2 o 3 non sono supportati.

---

#### HTTP/2 (2015 – standard moderno, diffusissimo)

- Mantiene **stessi metodi, status code, header** → semantica invariata.
- Cambia il **trasporto**:

    - **Un’unica connessione TCP** per più richieste.
    - **Multiplexing** → più richieste/risposte viaggiano in parallelo senza blocchi.
    
- **Header compression (HPACK)** → meno overhead, più velocità.
- **Server Push** → il server può inviare al client risorse **prima che le chieda** (es. CSS, JS).
- Usato quasi sempre con **HTTPS + TLS**.
- Molto più efficiente su reti moderne.

👉 Standard **di fatto oggi**, soprattutto con browser moderni e API.

---

#### HTTP/3 (2022 – in adozione crescente)

- Stessa logica di HTTP/2 (stessi metodi e concetti), ma cambia **protocollo di trasporto**.
    
- Basato su **QUIC** (Quick UDP Internet Connections), un protocollo su **UDP**.
- Vantaggi:
    
    - **Elimina il problema dell’Head of Line Blocking a livello TCP**.
    - **Connessioni più veloci**: handshake ridotto a 1-RTT (round trip time).
    - **Migliore resilienza** → non perde la connessione se cambia IP (utile su mobile, es. passaggio Wi-Fi → 4G).
    - **Perfetto per reti mobili e instabili**.
        
- Supportato da **Chrome, Firefox, Edge, Cloudflare, Google**.
    
👉 Ancora in crescita, ma sarà lo standard per streaming, app real-time e API performanti.

#### Tabella comparativa

| Caratteristica      | HTTP/1.1                | HTTP/2                      | HTTP/3 (QUIC)           |
| ------------------- | ----------------------- | --------------------------- | ----------------------- |
| Anno                | 1997                    | 2015                        | 2022                    |
| Connessione         | Multipli TCP            | Singolo TCP                 | Singolo UDP (QUIC)      |
| Parallelismo        | Limitato (Head-of-Line) | Multiplexing                | Multiplexing senza HOL  |
| Compressione Header | No                      | HPACK                       | QPACK                   |
| Server Push         | No                      | Sì                          | Sì                      |
| Sicurezza           | HTTPS opzionale         | HTTPS (praticamente sempre) | HTTPS integrato in QUIC |
| Performance         | Medio-basse             | Alte                        | Altissime, resilienti   |
| Supporto            | Universale              | Ampio (default browser/API) | In crescita rapida      |

### Sintesi:

> - **HTTP/1.1** → ancora diffuso, introduce Keep-Alive, chunked transfer, ma soffre di head-of-line blocking.
> - **HTTP/2** → migliora con multiplexing, header compression e server push; usato quasi sempre con TLS.
> - **HTTP/3** → basato su QUIC/UDP, elimina i limiti del TCP, è più veloce e stabile su reti moderne (perfetto per mobile e real-time).
## SOAP vs API vs REST

--- 
### 1. API (Application Programming Interface)

- In generale, **API = insieme di regole e metodi** che permettono a due applicazioni di comunicare.
- In ambito web, le API sono **endpoint HTTP** esposti da un server.
- Possono essere **SOAP** o **REST**.

> Tutte le REST API sono API, ma non tutte le API sono REST.

---

### 2. SOAP (Simple Object Access Protocol)

- Protocollo **standardizzato** per lo scambio di messaggi XML tra client e server.
- Basato su **XML** e **WSDL** (Web Services Description Language).
- Richiede **rigidità e strutture predefinite**: header, body, envelope.
- Supporta **WS-Security, transazioni, affidabilità avanzata**.
    
- Più pesante, meno flessibile rispetto a REST.

### Esempio base di messaggio SOAP

```xml
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope">
  <soap:Header/>
  <soap:Body>
    <GetCustomer xmlns="http://example.com/customers">
      <CustomerId>123</CustomerId>
    </GetCustomer>
  </soap:Body>
</soap:Envelope>
```

✅ Usato spesso in contesti enterprise o legacy (.NET WCF).

---

### 3. REST (Representational State Transfer)

- Architettura **stateless** basata su **HTTP**.
- Comunica tramite **URL, metodi HTTP e risposte JSON/XML**.
- Leggera, semplice da usare e scalabile.

- Ogni risorsa ha un URL unico (`/api/clienti/123`).
    

### Principi REST

- **Stateless** → server non mantiene stato tra richieste.
- **Client-server** → separazione chiara di responsabilità.
- **Uniform interface** → metodi standard (GET, POST, PUT, DELETE).
- **Layered system** → server e client possono essere separati in più layer.

### Esempio di richiesta REST

```http
GET /api/clienti/123 HTTP/1.1
Host: api.miosito.com
Accept: application/json
```

### Esempio di risposta REST


```http
{
  "id": 123,
  "nome": "Luca",
  "eta": 30
}
```

`-->` Tipico stack .NET moderno: **ASP.NET Web API** per creare REST API.

---

### Tabella comparativa

|Caratteristica|SOAP|REST|
|---|---|---|
|Formato dati|XML|JSON, XML, testo|
|Protocollo|SOAP + HTTP/SMTP|HTTP standard|
|Stato|Può essere stateful|Stateless|
|Sicurezza|WS-Security|HTTPS + token|
|Complessità|Alta|Bassa|
|Uso tipico|Enterprise, legacy|Web moderno, mobile|

---

###  Sintesi

> - **SOAP** = protocollo standard con XML, più pesante, usato in contesti enterprise.
> - **REST** = stile architetturale leggero, stateless, usa JSON e HTTP.
> - **API** = interfaccia tra applicazioni, può essere SOAP o REST.


# Virtual, Override e New in Csharp

### `virtual`

- Serve a **marcare un metodo** in una classe base come **sovrascrivibile**.
- Definisce un **punto di estensione**.
- Per default i metodi **non sono virtuali** in C#.

```csharp
class BaseClass
{
    public virtual void Saluta()
    {
        Console.WriteLine("Ciao dalla BaseClass");
    }
}
```

---

### `override`

- Permette a una **classe derivata** di ridefinire un metodo `virtual` (o `abstract`) della base.
- È un vero **polimorfismo**: la scelta del metodo avviene a **runtime** (binding dinamico).

```csharp
class Derivata : BaseClass
{
    public override void Saluta()
    {
        Console.WriteLine("Ciao dalla Derivata");
    }
}

BaseClass obj = new Derivata();
obj.Saluta(); // Output: "Ciao dalla Derivata"
```

👉 Anche se `obj` è di tipo `BaseClass`, viene eseguito il metodo di `Derivata`.

---

### `new`

- Serve a **nascondere** un metodo della classe base con la stessa firma.
    
- Non è polimorfismo: si chiama **method hiding**.
- La scelta del metodo dipende dal **tipo della variabile di riferimento**, non dall’oggetto reale.
    


```csharp
class DerivataNew : BaseClass
{
    public new void Saluta()
    {
        Console.WriteLine("Ciao dalla DerivataNew");
    }
}

BaseClass obj1 = new DerivataNew();
obj1.Saluta(); // Output: "Ciao dalla BaseClass"

DerivataNew obj2 = new DerivataNew();
obj2.Saluta(); // Output: "Ciao dalla DerivataNew"
```

👉 Se usi un riferimento di tipo base → esegue il metodo base.  
👉 Se usi un riferimento di tipo derivato → esegue il metodo derivato.

---

### Differenze chiave

|Parola chiave|Tipo di meccanismo|Risoluzione|Polimorfismo?|Caso d’uso|
|---|---|---|---|---|
|`virtual`|Abilitazione override|Runtime (dynamic)|✅ Sì|Estendere logica in sottoclassi|
|`override`|Ridefinizione metodo|Runtime (dynamic)|✅ Sì|Sovrascrivere comportamento base|
|`new`|Method Hiding|Compile-time (static)|❌ No|Nascondere metodo base senza polimorfismo|

---

### Sintesi

> In C#, `virtual` + `override` implementano il vero polimorfismo a runtime (binding dinamico).  
> `new` invece nasconde un metodo della base (method hiding), senza polimorfismo: il metodo chiamato dipende dal tipo del riferimento, non dall’oggetto reale.

# Verbi HTTP principali

## GET
- Serve per **richiedere dati** da un server.
- Non modifica lo stato del server → **idempotente e sicuro**.
- Tipico utilizzo: visualizzare pagine, elenchi, dettagli.
- Esempio in ASP.NET MVC:
```csharp
[HttpGet]
  public ActionResult Dettaglio(int id) 
  {
      var cliente = db.Clienti.Find(id);
      return View(cliente);
  }
```
URL esempio: `/Clienti/Dettaglio/5`

## POST
- Serve per **inviare dati al server** per <span style="color: #8392a4">creare una risorsa</span> o <span style="color: #8392a4">elaborare informazioni</span>.
- Non idempotente (==ripetere la richiesta può generare duplicati==).
- Tipico utilizzo: form di registrazione, creazione record.

Esempio:
```csharp
[HttpPost]
public ActionResult Crea(Cliente cliente) 
{
    db.Clienti.Add(cliente);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

## PUT

- Serve per **aggiornare completamente una risorsa** sul server.
- Idempotente (ripetere la richiesta produce lo stesso risultato).
- Tipico in Web API o servizi REST.

Esempio Web API:

```csharp
[HttpPut]
public IActionResult Aggiorna(int id, Cliente cliente) {
    db.Clienti.Update(cliente);
    db.SaveChanges();
    return NoContent();
}
```

## DELETE
- Serve per **cancellare** una risorsa sul server.
- Idempotente.
- Tipico in RESTful API.

Esempio:

```csharp
[HttpDelete]
public IActionResult Elimina(int id) {
    var cliente = db.Clienti.Find(id);
    db.Clienti.Remove(cliente);
    db.SaveChanges();
    return NoContent();
}
```

## Sintesi

**GET** → leggere dati (sicuro e idempotente)
**POST** → creare dati (non idempotente)
**PUT** → aggiornare dati (idempotente)
**DELETE** → cancellare dati (idempotente)

 `-->`  In ASP.NET MVC/Web API i verbi si mappano a metodi dei Controller tramite attributi [HttpGet], [HttpPost], [HttpPut], [HttpDelete].
Questo è fondamentale per sviluppare API RESTful o gestire correttamente form e richieste HTTP.

# Idempotenza HTTP

## Definizione

Un metodo HTTP è **idempotente** se **ripetere più volte la stessa richiesta produce lo stesso effetto sul server**, senza modificare ulteriormente lo stato dopo la prima esecuzione.

- In altre parole: fare 1, 2 o 100 richieste identiche → lo stato finale del server resta invariato.

---

## Esempi pratici

| Metodo | Idempotente? | Spiegazione |
|--------|--------------|------------|
| GET    | ✅ Sì        | Richiede dati, non modifica nulla. Ripetere GET non cambia lo stato del server. |
| POST   | ❌ No        | Crea nuove risorse o dati. Ripetere POST può creare duplicati. |
| PUT    | ✅ Sì        | Aggiorna una risorsa. Ripetere PUT con lo stesso contenuto mantiene lo stesso stato. |
| DELETE | ✅ Sì        | Elimina una risorsa. Ripetere DELETE sulla stessa risorsa non cambia ulteriormente lo stato (la risorsa è già cancellata). |
| PATCH  | ❌/✅ Dipende | Aggiorna parzialmente una risorsa; l’idempotenza dipende dall’implementazione. |

---

## Sintesi

L’**idempotenza** è importante nelle API REST perché permette di gestire retry di richieste senza rischiare effetti collaterali indesiderati. GET, PUT e DELETE sono idempotenti; POST no.

# Principi SOLID – Guida per Colloquio .NET

---
## Introduzione

I **principi SOLID** sono linee guida per scrivere **codice pulito, manutenibile e estensibile**.  
Sono cinque regole fondamentali proposte da Robert C. Martin (“Uncle Bob”).

**S.O.L.I.D.** =  
- **S** → Single Responsibility Principle  
- **O** → Open/Closed Principle  
- **L** → Liskov Substitution Principle  
- **I** → Interface Segregation Principle  
- **D** → Dependency Inversion Principle

---
## Single Responsibility Principle (SRP)

> Una classe dovrebbe avere una sola ragione per cambiare.

Ogni classe o modulo deve avere **una singola responsabilità**, cioè un unico scopo o motivo di modifica.

### ❌ Esempio sbagliato:
```csharp
public class OrderManager
{
    public void CreateOrder(Order order) { /* ... */ }
    public void SendEmail(Order order) { /* ... */ } // fa troppo
}
```

### ✅ Esempio corretto:

```csharp
public class OrderService {     public void CreateOrder(Order order) { /* logica ordine */ } }  public class EmailService {     public void SendEmail(Order order) { /* logica email */ } }
```

> SRP mi aiuta a mantenere il codice modulare e testabile. Se devo cambiare il modo in cui invio le email, non tocco la logica degli ordini.

## Open/Closed Principle (OCP)

> Le classi devono essere **aperte all’estensione** ma **chiuse alla modifica**.

Il codice dovrebbe permettere di aggiungere nuove funzionalità senza modificare quello esistente.

❌ Esempio sbagliato:
```csharp
public class PaymentProcessor
{
    public void Pay(string method)
    {
        if (method == "PayPal") { /* ... */ }
        else if (method == "CreditCard") { /* ... */ }
    }
}
```
✅ Esempio corretto (uso di polimorfismo):
```csharp
Copia codice
public interface IPayment
{
    void Pay();
}

public class PayPalPayment : IPayment
{
    public void Pay() { /* logica PayPal */ }
}

public class CreditCardPayment : IPayment
{
    public void Pay() { /* logica carta */ }
}

public class PaymentProcessor
{
    public void Process(IPayment payment) => payment.Pay();
}
```

> Aggiungo un nuovo metodo di pagamento senza toccare PaymentProcessor, rispettando l’OCP.

## Liskov Substitution Principle (LSP)

> Le classi **derivate** devono poter essere **usate al posto delle classi base** <span style="color: #8392a4">senza alterare il comportamento corretto</span>.

In pratica, ==una subclasse deve comportarsi come la superclasse, senza rompere le aspettative del codice chiamante==.

❌ Esempio sbagliato:
```csharp
Copia codice
public class Rectangle
{
    public virtual void SetWidth(double w) { Width = w; }
    public virtual void SetHeight(double h) { Height = h; }
}

public class Square : Rectangle
{
    public override void SetWidth(double w)
    {
        Width = Height = w; // rompe la logica del rettangolo
    }
}
```

✅ Esempio corretto:

Evita ereditarietà se rompe il comportamento; usa composizione o interfacce.

> Una sottoclasse deve rispettare il contratto della classe base. Se non può, probabilmente la gerarchia è sbagliata.

## Interface Segregation Principle (ISP)

> È meglio avere **molte interfacce specifiche** che una generica troppo grande.

Le classi non dovrebbero essere costrette a implementare metodi che non usano.

❌ Esempio sbagliato:
```csharp
public interface IWorker
{
    void Work();
    void Eat();
}

public class Robot : IWorker
{
    public void Work() { /* ok */ }
    public void Eat() { throw new NotImplementedException(); } // inutile
}
```

✅ Esempio corretto:
```csharp
Copia codice
public interface IWorkable { void Work(); }
public interface IEatable { void Eat(); }

public class Human : IWorkable, IEatable { /* ... */ }
public class Robot : IWorkable { /* ... */ }
```

> **Divido le interfacce per comportamento**, così i client implementano solo ciò che serve.

## Dependency Inversion Principle (DIP)

> <span style="color: #8392a4">Le classi devono dipendere da astrazioni</span>, non da implementazioni concrete.

Favorisci l’uso di interfacce e l’**iniezione delle dipendenze** (Dependency Injection).

❌ Esempio sbagliato:
```csharp
public class ReportService
{
    private SqlRepository _repo = new SqlRepository(); // dipendenza concreta
}
✅ Esempio corretto:
csharp
Copia codice
public class ReportService
{
    private readonly IRepository _repo;
    public ReportService(IRepository repo)
    {
        _repo = repo;
    }
}
```

> Uso **interfacce** e **dependency injection** per <span style="color: #8392a4">ridurre l’accoppiamento e facilitare il testing</span>.

## Riassunto rapido

|Principio|Significato|Beneficio|
|---|---|---|
|**S** – Single Responsibility|Una classe = una responsabilità|Manutenibilità|
|**O** – Open/Closed|Estendi senza modificare|Flessibilità|
|**L** – Liskov Substitution|Le sottoclassi rispettano la base|Correttezza|
|**I** – Interface Segregation|Interfacce piccole e specifiche|Semplicità|
|**D** – Dependency Inversion|Dipendi da astrazioni|Testabilità|

> I principi SOLID guidano la progettazione orientata agli oggetti:
> mantengono il codice modulare, scalabile e testabile.
> In .NET li applico con interfacce, iniezione delle dipendenze, separazione dei layer e attenzione alla singola responsabilità delle classi.”

