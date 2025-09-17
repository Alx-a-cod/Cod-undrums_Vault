---
tags:
  - obsidianFormatting
  - ObsidianUse
---

# Formattazione

La formattazione serve a **rendere il testo leggibile e organizzato** nelle tue note. 
Qui ci sono tutti i modi principali di evidenziare concetti o strutturare il contenuto

---

## 1️⃣ Grassetto

```markdown

**testo in grassetto**
Evidenzia concetti chiave o titoli minori all’interno del testo.

Utile per richiamare l’attenzione.
```

## 2️⃣ Corsivo
```markdown

*testo in corsivo*
Enfatizza parole o frasi.

Può essere combinato con il grassetto: ***grassetto e corsivo***.

```

## 3️⃣ Barrato
```markdown

~~testo barrato~~
Indica concetti superati o non più validi.

Utile nelle note in progressione o revisioni.

```

## 4️⃣ Evidenziato
``` markdown

==testo evidenziato==
Utile per ricordare concetti importanti.

Può essere combinato con grassetto o corsivo.

```

## 5️⃣ Blocchi di citazione
```markdown

> Questo è un blocco di citazione
Per definizioni, note importanti o commenti.

Spesso usato con callout per maggiore visibilità.

```

## 6️⃣ Callout

**Tipi di callout:** note, tip, warning, important, bug.
            Possono contenere anche codice o immagini.        
##### Callout "note"
```markdown

> [!note] Nota
> Questo è un consiglio utile o un avviso importante.
> > Puoi aggiungere **testo in grassetto**, *corsivo* o ==evidenziato==.

```
- **Icona:** 📝
- **Uso:** informazioni generali, appunti importanti ma neutri.

##### Callout "tip"

```markdown

> [!tip] Suggerimento
> Questo è un trucco o consiglio pratico.
> 
> Ad esempio:
> ```csharp
> Console.WriteLine("Tip: usa sempre il debugger!");

```
- **Icona:** 💡
- **Uso:** consigli pratici o *“best practice”*.

##### Callout "warning"

```markdown

`> [!warning] Attenzione > Questo è un avviso o pericolo da non ignorare. > Puoi aggiungere anche un’immagine: > ![Immagine warning](./immagini/alert.png)`

```
- **Icona:** ⚠️
- **Uso:** warning, rischi, errori comuni.

##### Callout “important”

```markdown

> [!important] Importante
> Questo è un concetto chiave o priorità alta.
> Non dimenticare di applicarlo!

```

- **Icona:** ❗
- **Uso:** informazioni cruciali o da ricordare assolutamente.
##### Callout “bug”

> [!bug] Bug
> Segnala un errore, problema o comportamento strano nel codice.
> Esempio:
> ```csharp
> // BUG: questo metodo lancia eccezioni quando l'array è vuoto
> ```

- **Icona:** 🐛
- **Uso:** errori, problemi noti o comportamenti inattesi.

###### ✅ Note pratiche sui callout
- Possono contenere **tutti gli elementi Markdown**: liste, codice, link, immagini, grassetto, corsivo.
- Si possono nidificare all’interno di note più grandi.
- Mantieni **un solo tipo per callout**, così è chiaro visivamente nel Graph View e nell’editing.

## 7️⃣ Blocchi di codice

#### Inline
```markdown
Usa `Console.WriteLine()` per stampare.
```

#### Multi-linea
csharp
Copia codice
Console.WriteLine("Hello World");
Supporta syntax highlighting per diversi linguaggi (C#, Python, JS, ecc.).

Utile per esempi pratici o snippet da ricordare.

8️⃣ Tabelle
markdown
Copia codice
| Concetto     | Esempio          |
|--------------|----------------|
| Grassetto    | **testo**      |
| Corsivo      | *testo*        |
| Evidenziato  | ==testo==      |
Permette di comparare formati o dati strutturati.

9️⃣ Liste
Liste puntate - elemento

Liste numerate 1. elemento

Liste nidificate - sotto-elemento

✅ Consigli pratici
Usa grassetto per concetti chiave.

Corsivo per enfasi o parole straniere.

Evidenziato per concetti da ricordare subito.

Callout e citazioni per note importanti o avvisi.

lua
Copia codice

Con questa nota:

- Puoi **collegare [[Titoli]], [[Liste]], [[Markup]]** come concetti correlati.  
- Diventa un nodo nel Graph View molto chiaro e utile.  
- Include esempi pratici Markdown e C#.  

---