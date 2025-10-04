---
tags:
  - NET
---


# Differenze tra .NET Framework e .NET Core

---
### 1. .NET Framework

- **Anno**: introdotto nel 2002.
- **Obiettivo**: sviluppare applicazioni **Windows-centriche**.
- **Compatibilità**: integrato nativamente con Windows e i prodotti Microsoft (es. IIS, Windows Forms, WPF).
    
- **Ambiti d’uso**:
    - Applicazioni Desktop Windows (WinForms, WPF).
    - ASP.NET Web Forms e MVC su IIS.
    - Servizi Windows.
        
- **Distribuzione**: installato come **runtime sul sistema operativo Windows**.
- **Limitazione principale**: <span style="color: #8392a4">non multipiattaforma</span> → solo Windows.
- **Stato attuale**: è <span style="color: #8392a4">legacy</span>, ancora supportato ma **non più evoluto** (ultimo major: .NET Framework 4.8).
    

---

### 2. .NET Core

- **Anno**: rilasciato nel 2016.
- **Obiettivo**: piattaforma **open source e multipiattaforma** (Windows, Linux, macOS).
    
- **Ambiti d’uso**:
    - ASP.NET Core per Web API, microservizi, applicazioni cloud.
    - Console app cross-platform.
    - Integrazione con Docker/Kubernetes.
        
- **Caratteristiche principali**:
    - **Open Source** (su GitHub).
    - **Performance superiori** rispetto a .NET Framework.
    - **Deployment flessibile** → side-by-side (più versioni di runtime sulla stessa macchina).
    - **Portabilità** → stesso codice gira su OS diversi.
        
- **Stato attuale**: è stato il passo intermedio che ha portato a **.NET 5+**.
---

### 3. Evoluzione: .NET 5, 6, 7, 8

- Dal 2020 Microsoft ha **unificato** Framework e Core in un’unica piattaforma: **.NET 5+**.
- **.NET 6 (LTS)** e successivi (.NET 7, .NET 8 LTS) sono l’**evoluzione naturale di .NET Core**.
- Supportano **web, desktop, mobile (MAUI), IoT, cloud, AI**.
- Tutto passa da **ASP.NET Core** (non esiste più ASP.NET “vecchio”).

---

### Differenze chiave (tabella comparativa)

|Caratteristica|.NET Framework|.NET Core / .NET 5+|
|---|---|---|
|OS supportati|Solo Windows|Windows, Linux, macOS|
|Stato|Legacy (4.8, no evoluzione)|Attivo, in continua evoluzione|
|Open Source|No|Sì|
|Performance|Buone ma limitate|Alte, ottimizzate|
|Deployment|Runtime globale|Side-by-side, self-contained|
|App desktop|WinForms, WPF|WinForms, WPF (solo Windows), MAUI multipiattaforma|
|App web|ASP.NET (WebForms, MVC)|ASP.NET Core (Web API, Blazor, Razor Pages)|
|Cloud & container|Limitato|Ottimizzato per cloud-native|

---

### Sintesi:

> - **.NET Framework**: nato per applicazioni Windows, integrato in Microsoft stack, oggi legacy (solo Windows, ultima versione 4.8).
> - **.NET Core**: multipiattaforma, open source, performance migliori, orientato al web/cloud.
> - **.NET 5+**: unificazione → oggi si parla solo di “.NET”, che ha inglobato Core e sostituito Framework per tutti i nuovi sviluppi.

>**“cosa si usa oggi”**, risposta
>
 → **.NET 6/8** (Core evoluto) perché multipiattaforma, moderno e supportato a lungo termine (**LTS**).  
>Il **Framework** rimane solo per applicazioni legacy Windows già esistenti.


# ASP.NET WebForms

## Concetti Generali

 **ASP.NET WebForms** è stato uno dei primi framework Microsoft per lo sviluppo di applicazioni web.  
Si basa su un **modello event-driven** simile a quello delle applicazioni desktop (Windows Forms).  

L’obiettivo era semplificare il passaggio degli sviluppatori dal desktop al web, fornendo strumenti come:  
- <span style="color: #8392a4">Server-side rendering</span>
- <span style="color: #8392a4">Controlli server</span> (`runat="server"`) 
- <span style="color: #8392a4">Gestione automatica dello stato</span>

## Page Life Cycle
Ogni pagina segue un ciclo di vita definito con eventi come:  
- **Init** → inizializzazione controlli e proprietà  
- **Page_Load** → logiche eseguite a ogni caricamento  
- **PreRender** → modifiche finali prima del rendering  
- **Unload** → rilascio risorse  

- Concetto centrale → **Postback**: ogni interazione dell’utente (es. click su pulsante) provoca un invio della pagina al server, che la elabora e ne restituisce una nuova versione renderizzata.  

 Quindi il server elabora i dati e restituisce una **nuova versione della pagina** al client.  
- Flusso tipico:
  1. Caricamento pagina  
  2. Gestione eventi dei controlli  
  3. Elaborazione lato server  
  4. Rendering della pagina aggiornata  
     
- **Callback/AJAX (UpdatePanel, ScriptManager)** → consente aggiornamenti parziali della pagina senza ricaricarla tutta.  

## Gestione Stato
Poiché HTTP è **stateless** (ogni richiesta è indipendente), WebForms introduce diversi meccanismi:  

- **ViewState** (mantiene i valori dei controlli tra le richieste → aumenta peso pagina)  
- **Session State** (mantiene informazioni lato server per ogni utente)  
- **Application State** (variabili condivise da tutti gli utenti)  
- **Cookies** e **QueryString**  per memorizzazione lato client e passaggio dati tramite URL. 
  
## Server Controls vs HTML Controls:  

  - Con `runat="server"` diventano oggetti lato server manipolabili in C#.  
  - Permettono gestione eventi, stato e rendering dinamico.
## Strumenti di Struttura
- **Code-behind model**: Separazione tra la logica (C# in `.aspx.cs`) e la presentazione (HTML/ASP in `.aspx`).  
  Favorisce la manutenzione ma tende a mescolare ancora UI e logica di business (non è MVC).
- **Master Pages**: layout comuni per più pagine.  
- **User Controls**: componenti riutilizzabili.  
- **Data Binding**: controlli come `GridView` o `Repeater` si collegano direttamente a sorgenti dati.  
- **Event Bubbling**: propagazione eventi dai controlli figlio alla pagina.  

## Shared 

- shared è una **cartella speciale** all'interno della directory **Views** che contiene viste condivise tra più controller. Queste viste possono includere ==layout comuni, parziali o componenti riutilizzabili che vengono utilizzati in diverse parti dell'applicazione==.

- Un **layout principale** che definisce la struttura generale della pagina (header, footer, menu di navigazione) può essere memorizzato nella cartella Shared e utilizzato da tutte le viste dell'applicazione.

>Se voglio una pagina diversa per ogni controller, non la metto in shared, ma nella cartella del controller specifico.
>Quindi se ho necessità di avere una pagina diversa per ogni controller, non la metto in shared, ma nella cartella del controller specifico.

Se invece voglio una pagina condivisa tra più controller, la metto in shared.

Esempio: 
<span style="color: #8392a4">Layout.cshtml</span> è un file di layout condiviso che definisce la struttura generale della pagina, come l'header, il footer e il menu di navigazione.
<span style="color: #8392a4">ValidationScriptsPartial.cshtml</span> è un file parziale che include gli script di validazione per i moduli, utilizzato in diverse viste dell'applicazione.
Una vista non condivisa che magari può essere usata solo in un controller specifico, la metto nella cartella del controller specifico, ad esempio Views/Home/Index.cshtml.
## Evoluzione e Limiti
- WebForms ha introdotto strumenti come **ScriptManager** e **UpdatePanel** per supportare AJAX e aggiornamenti parziali.  
- Tuttavia rimane **pesante e poco flessibile**:  
  - ViewState aumenta il peso della pagina  
  - markup generato poco controllabile  
  - non segue pattern moderni come MVC/MVVM  
  - non adatto a SPA e applicazioni moderne scalabili  

Oggi WebForms è considerato un **framework legacy**, sostituito da **ASP.NET MVC** e poi da **ASP.NET Core**.  

---

## Punti di Ingresso
- **`default.aspx`** → tipicamente la pagina iniziale di un’app WebForms, ma l’ingresso può essere configurato nel `web.config`.  
- **`Global.asax`** → file di applicazione che gestisce eventi globali come:  
    - `Application_Start` → inizializzazione all’avvio dell’applicazione  
    - `Session_Start` → creazione nuova sessione utente  
    - `Application_Error` → gestione errori globali  
    - `Application_End` → chiusura dell’applicazione  

---

## Sintesi:

>WebForms è un framework Microsoft per applicazioni web basato su eventi, molto simile a Windows Forms. 
>Ogni pagina ha un **ciclo di vita ben definito**, e il concetto chiave è il **postback**, che, insieme al **callback** invia la pagina al server a ogni interazione.
>Per gestire la natura stateless di HTTP utilizza strumenti come **ViewState**, **Session** e **Application State**. 
>
>Ha introdotto la separazione tra **markup e code-behind**, oltre a Master Pages e User Controls. 
>Tuttavia, a causa di pesantezza e scarsa flessibilità, oggi è considerato legacy ed è stato rimpiazzato da **MVC e ASP.NET Core**.  
>
>Il punto di ingresso tipico è `default.aspx`, mentre `Global.asax` serve a gestire eventi globali come avvio applicazione, sessioni e errori.  


# Pattern MVC in .NET

## Concetto generale
- **MVC (Model-View-Controller)** è un pattern architetturale che separa un’applicazione in 3 livelli:
  - **Model** → dati e logica di business (accesso al DB, validazioni, calcoli).
  - **View** → presentazione grafica dei dati.
  - **Controller** → gestisce le richieste dell’utente, invoca il Model e aggiorna la View.

Questo approccio aumenta la **modularità**, favorisce la **testabilità** e rende l’applicazione **scalabile** e più semplice da manutenere.

---
## Model
- Contiene la logica di business e di accesso ai dati, e la logica di business dell'applicazione. Gestisce l'accesso ai dati a DB o altro, le regole di validazione e le operazioni di manipolazione dei dati.
- Può includere entità, DTO, repository e servizi.
- è una interpretazione intermedia dei dati, che può intervenire sui i dati grezzi del DB con logica applicativa, arricchiendoli talvolta anche con informazioni non reperibili dalla base dati. 


---

## View
- Responsabile della **presentazione dati** all'utente.
- Può essere una pagina Razor, un file .cshtml o altro.
- Deve essere **“stupida”**: non dovrebbe contenere logica complessa, solo binding e layout.

---

## Controller
- Funge da intermediario tra il modello e la vista. Riceve le richieste dell'utente, elabora i dati tramite il modello e aggiorna la vista di conseguenza.
- Sono rappresentati all'interno del framework come classi estratte che ereditano da una classe base chiamata Controller.
- Riceve le richieste HTTP, esegue la logica tramite il **Model** e restituisce la **View** o un **JSON/API result**.
- Convenzione: se ho `HomeController`, la view corrispondente è in `Views/Home`.

---

## Punti di ingresso
- In **ASP.NET MVC** non esiste un unico `default.aspx` come in WebForms.
- L’ingresso è gestito dal **Global.asax** + **Routing**:
  - `Global.asax` → gestisce il ciclo di vita dell’applicazione (Application_Start, Session_Start, ecc.).
  - `RouteConfig.cs` → definisce le regole di routing, cioè mappare URL → Controller/Action.
- Esempio route default:
```csharp
  routes.MapRoute
  (
      name: "Default",
      url: "{controller}/{action}/{id}",
      defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
  );
```
# Bootstrap & ASP.NET MVC

## Bootstrap
- **Bootstrap** è un framework **CSS open-source** usato per creare interfacce moderne e **responsive**.  
- Offre componenti predefiniti: **griglie, pulsanti, moduli, navbar, alert** ecc.  
- Usa un **grid system** basato su righe e colonne (mobile-first), adattabile a qualsiasi schermo.  
- Semplifica lo sviluppo front-end, permettendo un design coerente e veloce.

### Sintesi:  
>Bootstrap è un framework CSS che semplifica lo sviluppo di UI responsive. 
>Fornisce un sistema a griglia e **componenti già pronti**, riducendo i tempi di sviluppo e garantendo compatibilità **cross-device**.

---
## TempData in ASP.NET MVC

- **TempData** è un dizionario (`TempData["chiave"]`) che permette di **passare dati temporanei tra due richieste consecutive**.  

- Usato per:

  - Messaggi di conferma (`TempData["Success"] = "Utente creato con successo!"`).
  - Notifiche di errore o redirect dopo un `Post-Redirect-Get`.  
- I dati in **TempData vengono eliminati automaticamente** dopo la prima lettura.  

- Internamente usa **Session state**, ma con durata limitata.

### Sintesi: 

>TempData serve a mantenere dati temporanei tra due richieste consecutive. Lo uso spesso per passare messaggi di stato o notifiche dopo un redirect. Diverso da ViewBag (solo per la stessa richiesta) e Session (persistente fino alla chiusura).
