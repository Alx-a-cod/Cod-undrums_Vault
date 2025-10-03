
La **Programmazione Orientata agli Oggetti (Object-Oriented Programming, OOP)** è un paradigma di programmazione basato sull’uso di **oggetti** come unità fondamentali di costruzione del software.  
Un oggetto è un’istanza di una **classe**, che rappresenta un modello astratto con **proprietà** (dati/attributi) e **metodi** (funzionalità/comportamenti).

---

## Concetti Fondamentali dell’OOP

1. **Classe**
   - È un modello o schema che definisce le caratteristiche e i comportamenti degli oggetti.
   - In C# si definisce con la keyword `class`.

2. **Oggetto**
   - È un’<span style="color: #8392a4">istanza concreta</span> di una classe.
   - Contiene i <span style="color: #8392a4">dati reali</span> e può ==eseguire azioni definite dalla classe.==

-  [[1.a Incapsulamento (Encapsulation)]]
   - Protezione dei dati all’interno della classe.
   - Accesso controllato tramite **modificatori di accesso** (`public`, `private`, `protected`, `internal`).
   - Espone solo ciò che è necessario (principio di **information hiding**).

-  [[1.b Ereditarietà]]
   - Una classe può **ereditare** proprietà e metodi da un’altra classe.
   - Favorisce il riuso del codice.
   - In C#: `class Studente : Persona { }`

-  [[1.c Polimorfismo]]
   - Una stessa interfaccia o metodo può avere implementazioni differenti.
   - Due forme:
     - **Polimorfismo di overload** (stesso nome, parametri diversi).
     - **Polimorfismo di override** (metodo ridefinito in una sottoclasse).
   - Supportato in C# tramite `virtual`, `override`, `abstract`, `interface`.

- [[1.d Astrazione]]
   - Esporre solo gli aspetti essenziali di un oggetto, nascondendo i dettagli implementativi.
   - Si realizza tramite **classi astratte** e **interfacce**.

---

## Esempio in C#
```csharp
// Classe base (astrazione di una Persona)
public class Persona
{
    public string Nome { get; set; }
    public int Eta { get; set; }

    public virtual void Saluta()
    {
        Console.WriteLine($"Ciao, mi chiamo {Nome}");
    }
}

// Classe derivata (ereditarietà)
public class Studente : Persona
{
    public string Matricola { get; set; }

    // Polimorfismo tramite override
    public override void Saluta()
    {
        Console.WriteLine($"Ciao, sono {Nome} e la mia matricola è {Matricola}");
    }
}

// Uso
Persona p = new Persona { Nome = "Mario", Eta = 30 };
p.Saluta();

Studente s = new Studente { Nome = "Luca", Eta = 20, Matricola = "12345" };
s.Saluta();
```
### Perché è importante l’OOP in .NET

- Organizza il codice in maniera **modulare** e **riutilizzabile**.
- Favorisce la **manutenibilità** e la **scalabilità**.
- Permette di sfruttare appieno i framework .NET (che sono progettati in OOP).

È alla base di **design pattern** e **architetture moderne** (MVC, MVVM, Clean Architecture).

### In sintesi :

La programmazione orientata agli oggetti in C# consente di **modellare** il mondo reale attraverso **classi** e **oggetti**, applicando i principi di [[1.a Incapsulamento (Encapsulation)]], [[1.b Ereditarietà]], [[1.c Polimorfismo]] e [[1.d Astrazione]], per scrivere codice pulito, riusabile e scalabile.

