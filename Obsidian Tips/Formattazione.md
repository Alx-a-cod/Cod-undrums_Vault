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

- **Aspetto grafico:** **Questo è importante, non puoi ignorarlo!**

## 2️⃣ Corsivo
```markdown

*testo in corsivo*
Enfatizza parole o frasi.

Può essere combinato con il grassetto: ***grassetto e corsivo***.

```

- **Aspetto grafico:** *elegante, non troppo invadente.*

## 3️⃣ Barrato
```markdown

~~testo barrato~~ <-- doppia tilde num: alt + 0126 
Indica concetti superati o non più validi.

Utile nelle note in progressione o revisioni.

```

- **Aspetto grafico:** ~~oopsie, questo non dovevo esserci~~

## 4️⃣ Evidenziato
``` markdown

==testo evidenziato==
Utile per ricordare concetti importanti.

Può essere combinato con grassetto o corsivo.

```

- **Aspetto grafico:** ==Hey, LISTEN!==

## 5️⃣ Blocchi di citazione
```markdown

> Questo è un blocco di citazione
Per definizioni, note importanti o commenti.

Spesso usato con callout per maggiore visibilità.

```

- **Aspetto grafico:**
> come disse un famosissimo saggio...

## 6️⃣ Callout

**Tipi di callout:** note, tip, warning, important, bug.
            Possono contenere anche codice o immagini.        
            
##### Callout "note"
```markdown

> [!note] Nota
> Questo è un consiglio utile o un avviso importante.
> > Puoi aggiungere **testo in grassetto**, *corsivo* o ==evidenziato==.

```

- **Aspetto grafico:** 
> [!note] Nota:
> Obsidian non ama gli spazi nei nomi dei file. Come i gatti, preferisce le cose compatte.
- **Icona:** 📝
- **Uso:** informazioni aggiuntive, chiarimenti, dettagli secondari.

##### Callout "tip"

```markdown

> [!tip] Suggerimento
> Questo è un trucco o consiglio pratico.
> 
> Ad esempio:
> ```csharp
> Console.WriteLine("Tip: usa sempre il debugger!");

```

- **Aspetto grafico:** 
> [!tip] Lo sapevi?
> Usa `Ctrl + P` per cercare tutto. Anche la tua pazienza.
- **Icona:** 💡
- **Uso:** suggerimenti utili, scorciatoie, best practice.

##### Callout "warning"

```markdown

> [!warning] Attenzione 
> Questo è un avviso o pericolo da non ignorare. 
> Puoi aggiungere anche un’immagine: 
> ![Immagine warning](./immagini/alert.png)`

```

- **Aspetto grafico:** 
> [!warning] Attenzione!
>Non è un dramma, ma quasi. Procedi con cautela, o con un backup.
> ```bash
> rm -rf /  
> // ...e così finì la leggenda del tuo disco rigido.
> ```
- **Icona:** ⚠️
- **Uso:** rischi, comportamenti pericolosi, cose che potrebbero andare storte e che probabilmente lo faranno.

##### Callout “important”

```markdown

> [!important] Importante
> Questo è un concetto chiave o priorità alta.
> Non dimenticare di applicarlo!

```

- **Aspetto grafico:** 
  > [!important] Importante
  > Se leggi solo una cosa oggi, che sia questa. Il resto è contorno.
  > ```markdown
  > // I file `.md` sono il cuore pulsante di Obsidian. Non toccarli con editor strani.
  > ```
- **Icona:** ❗
- **Uso:** - concetti fondamentali, avvisi critici, cose da ricordare sempre.
- 
##### Callout “bug”
```markup
> [!bug] Bug
> Segnala un errore, problema o comportamento strano nel codice.
> Esempio:
> ```csharp
> // BUG: questo metodo lancia eccezioni quando l'array è vuoto
> ```
```

- **Aspetto grafico:** 
> [!bug] Bug
>Il codice è lì, ti fissa, e tu non capisci perché non funziona.
> ```js
> const user = null;
> console.log(user.name); // TypeError: Cannot read property 'name' of null
> ```
- **Icona:** 🐛
- **Uso:** errori, problemi noti o comportamenti inattesi.

###### ==✅ Note pratiche sui callout==
- Possono contenere **tutti gli elementi Markdown**: liste, codice, link, immagini, grassetto, corsivo.
- Si possono nidificare all’interno di note più grandi.
- Mantieni **un solo tipo per callout**, così è chiaro visivamente nel Graph View e nell’editing.

## 7️⃣ Blocchi di codice

#### Inline
```markdown / bash / js / py / csharp / c / chi più ne ha più ne metta
Usa `Console.WriteLine()` per stampare.
```

#### Multi-linea
csharp
Console.WriteLine("Hello World");
Supporta syntax highlighting per diversi linguaggi (C#, Python, JS, ecc.).

Utile per esempi pratici o snippet da ricordare.

8️⃣ Tabelle
markdown
Copia codice
| Concetto      | Esempio          |
|--------------    |----------------|
| Grassetto     | **testo**      |
| Corsivo         | *testo*        |
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

---