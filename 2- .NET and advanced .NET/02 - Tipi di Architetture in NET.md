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


# Architettura a Microservizi – Guida per Colloquio (livello .NET junior)

---

## Cos’è un microservizio
Un **microservizio** è un **piccolo modulo indipendente** di un’applicazione che:
- realizza **una singola funzionalità di business** (es. “Gestione Ordini”),
- ha **database, logica e API proprie**,
- comunica con gli altri servizi tramite **API REST** o **messaggistica** (es. RabbitMQ, Kafka),
- può essere **sviluppato, distribuito e scalato indipendentemente**.

In breve:
> 👉 “Un microservizio è un’applicazione autonoma, piccola e focalizzata, che collabora con altre per formare un sistema completo.”

---

## Struttura tipica di un microservizio .NET

Esempio: **Servizio Ordini**

/OrderService
├── OrderService.API/ → API REST (ASP.NET Core)
│ ├── Controllers/
│ ├── DTOs/
│ ├── Program.cs
│ └── appsettings.json
├── OrderService.Core/ → Logica di dominio, entità, interfacce
├── OrderService.Infrastructure/ → Accesso dati (EF Core, Dapper), repository
├── OrderService.Tests/ → Unit test e integrazione


Tecnologie comuni in .NET:
- **ASP.NET Core** → API REST  
- **Entity Framework Core / Dapper** → accesso dati  
- **Serilog / Application Insights** → logging  
- **MediatR / CQRS pattern** → separazione dei comandi e query  
- **Docker** → containerizzazione  
- **Kubernetes** → orchestrazione  
- **API Gateway** (es. Ocelot o YARP) → punto d’ingresso unico

---

## Componenti principali dell’architettura

| Componente | Descrizione |
|-------------|-------------|
| **Service** | Microservizio autonomo con logica di business specifica |
| **Database per servizio** | Ogni microservizio ha il suo schema o database (no DB condiviso) |
| **API Gateway** | Gestisce routing, autenticazione, bilanciamento (es. Ocelot, YARP) |
| **Service Discovery** | Rilevamento automatico di endpoint (es. Consul, Eureka) |
| **Message Broker** | Comunicazione asincrona tra servizi (RabbitMQ, Kafka, Azure Service Bus) |
| **Container / orchestratore** | Docker + Kubernetes per eseguire e scalare i servizi |
| **CI/CD pipeline** | Automazione build → test → deploy di ogni microservizio |

---

## Comunicazione tra microservizi

### 1. Sincrona (REST API)

Ogni servizio espone API HTTP:

```http
GET /api/clienti/1
POST /api/ordini
```

> Facile da implementare, ma più fragile (se un servizio non risponde → fallback, retry).
### 2. Asincrona (eventi / messaggi)

- Usa **message broker** (es. RabbitMQ, Azure Service Bus, Kafka).
- I servizi si inviano eventi (“OrdineCreato”) → disaccoppiamento totale.

Migliora scalabilità e resilienza.
##### Vantaggi dei microservizi
✅ Scalabilità indipendente (es. scalare solo OrderService).
✅ Deploy separati → meno impatti sugli altri moduli.
✅ Allineamento ai team (team autonomi per servizio).
✅ Tecnologie diverse possibili per ogni microservizio.
✅ Maggiore resilienza (guasto di un servizio non ferma tutto).

##### Svantaggi e complessità
- Gestione distribuita → logging, tracing e debugging più complessi.
- Sicurezza → autenticazione e autorizzazione per ogni servizio.
- Dati duplicati → serve coerenza eventuale, non transazioni cross-service.
- Deploy coordinato → serve CI/CD ben configurata.
- Osservabilità → servono strumenti centralizzati (Elastic Stack, Prometheus, Grafana).

#### Pattern architetturali usati

|Pattern|Scopo|
|---|---|
|**API Gateway**|Centralizza accesso, sicurezza e routing|
|**CQRS (Command Query Responsibility Segregation)**|Divide lettura/scrittura per performance|
|**Event-driven**|Comunicazione tramite eventi per disaccoppiamento|
|**Saga / Outbox**|Gestione di transazioni distribuite|
|**Circuit Breaker / Retry**|Evita propagazione di errori tra servizi|
|**Service Discovery**|Localizza dinamicamente gli endpoint dei servizi|

#### Database per microservizio

Ogni servizio gestisce i propri dati:

- nessuna condivisione diretta di tabelle tra microservizi;
- scambio solo via API o messaggi.

Esempio:

```bash 
CustomerService → CustomersDb
OrderService → OrdersDb
```

Per sincronizzare dati:

- usare eventi (“CustomerUpdated”) o event sourcing oppure **cache condivise** (es. Redis).

### Microservizi in Docker e Kubernetes

Ogni microservizio viene:

- **containerizzato** (Dockerfile)
- pubblicato come **immagine**
- **orchestrato** da Kubernetes tramite *Deployment* + *Service*.

Esempio:

```bash
docker build -t order-service .
docker run -p 8080:80 order-service
```
Poi:

```bashCopia codice
kubectl apply -f order-deployment.yaml
```

 Esempio pratico (workflow .NET)
 
`-->` OrderService riceve **POST** /api/ordini
`-->` **Valida** e *salva* nel ==proprio== database
`-->` **Pubblica evento** OrdineCreato su **message bus**
`-->` **EmailService ascolta l’evento** e invia email di conferma
`-->` InventoryService **aggiorna** le quantità disponibili

→ Servizi indipendenti, coordinati da eventi, con coerenza eventuale.

💬 Domande tipiche in colloquio

|Domanda|Risposta sintetica ideale|
|---|---|
|Cos’è un microservizio?|Piccola app autonoma che realizza una funzionalità e comunica con le altre via API o eventi.|
|Differenza tra monolite e microservizi?|Nel monolite tutto è unito in un unico processo; nei microservizi le parti sono separate e indipendenti.|
|Come comunicano tra loro?|Tramite API REST o messaggi asincroni.|
|Cosa succede se un servizio non risponde?|Implemento retry, timeout o circuit breaker.|
|Come gestisci la sicurezza?|JWT o OAuth2, API Gateway per autenticazione centralizzata.|
|Ogni microservizio deve avere il suo DB?|Sì, per evitare coupling. Si usa sincronizzazione via eventi.|
|Come si scala un microservizio?|Verticalmente (più risorse) o orizzontalmente (più repliche, es. in Kubernetes).|
|Come gestisci log e tracing?|Logging centralizzato (Serilog + Elastic, Application Insights, OpenTelemetry).|

#### Riassunto

Un sistema a microservizi è come un insieme di API autonome, ciascuna:
- indipendente nel deploy e nello sviluppo,
- incapsulata nel proprio dominio,
- orchestrata in modo automatizzato (Docker/Kubernetes),
- monitorata e connessa tramite eventi.

> Nel monolite hai un’app grande e unica. Nei microservizi hai tante piccole app che collaborano in rete.

