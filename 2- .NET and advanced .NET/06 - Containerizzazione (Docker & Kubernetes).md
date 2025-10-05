
### Containerizzazione – Docker & Kubernetes

#### 1. Cos’è la containerizzazione?

- Tecnica per **impacchettare applicazioni e dipendenze** in un contenitore isolato.  

- Differenza con **macchine virtuali (VM)**:
  - VM: simulano un intero OS, pesanti.
  - Container: condividono il kernel dell’host, più leggeri e veloci.
  
- Vantaggi:
  - Portabilità → “funziona ovunque” (dev, test, prod).
  - Scalabilità e deploy veloci.
  - Isolamento → un’app non interferisce con un’altra.

---

#### 2. Docker – Fondamenti

- **Dockerfile** → descrive come costruire l’immagine del container.
- **Immagine** → template immutabile con codice + dipendenze.
- **Container** → istanza in esecuzione di un’immagine.
- **Registry** (es. DockerHub, ACR, ECR) → archivio immagini.

- **Comandi base**
```bash
docker build -t myapp:1.0 .
docker run -d -p 8080:80 myapp:1.0
docker ps
docker logs <containerId>
```

#### 3. Kubernetes – Orchestrazione

Sistema di orchestrazione per gestire più container in cluster.

Funzionalità principali:
- **Deployment**: gestione versioni, rollout, rollback.
- **Scalabilità automatica**: scale up/down in base al carico.
- Service Discovery & Load Balancing.
- **Self-healing**: riavvia container in crash, ribilancia.

Oggetti fondamentali:
- **Pod** → unità base (1+ container).
- **Service** → espone Pod (interno o esterno).
- **Deployment** → gestisce replica e aggiornamenti dei Pod.
- **ConfigMap/Secret** → configurazioni e credenziali.
- **Ingress** → routing HTTP esterno.

Esempio yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myregistry/myapp:1.0
        ports:
        - containerPort: 80
```

#### 4. Docker vs Kubernetes

**Docker** → gestisce singolo container/app.
**Kubernetes** → orchestration di cluster con centinaia/migliaia di container.

Spesso usati insieme:

- <span style="color: #8392a4">Docker per build & run</span>.
- <span style="color: #20bf6b">Kubernetes per deploy & scalabilità</span>.

#### 5. Best Practice

- Scrivere Dockerfile ottimizzati (layer minimi, immagini leggere).
- Usare variabili d’ambiente/config esterne → niente credenziali hardcoded.
- Log e metriche centralizzate.
- Security: non girare come root dentro container.
- CI/CD: integrare Docker/K8s nei pipeline (es. Azure DevOps, GitHub Actions).
- Monitoraggio con Prometheus, Grafana, ELK/EFK.


# 💬 Domande su Docker

## 1. Cos’è Docker e a cosa serve?

Docker è una piattaforma per creare, distribuire ed eseguire applicazioni in **container**, ovvero ambienti isolati che includono tutto il necessario per far funzionare il software (codice, librerie, dipendenze, configurazioni).

---

## 2. Qual è la differenza tra un container e una macchina virtuale?

Una **VM** virtualizza un intero sistema operativo, mentre un **container** condivide il kernel dell’host e virtualizza solo l’ambiente dell’applicazione.  
 I container sono più **leggeri**, **veloci da avviare** e **più portabili**.

---

## 3. Cosa contiene un’immagine Docker?

Un’immagine è un **template immutabile** che contiene il codice dell’app, le librerie e la configurazione necessaria per creare un container.  
È composta da **layer** sovrapposti (filesystem a livelli).

---

## 4. Come si crea un’immagine personalizzata?

Tramite un file `Dockerfile`, che specifica i comandi necessari (es. `FROM`, `COPY`, `RUN`, `EXPOSE`, `CMD`) per costruire l’immagine.  
 Si usa poi il comando:
 
> ```bash
> docker build -t nome-immagine .
> ```

---

## 5. Qual è la differenza tra `RUN`, `CMD` e `ENTRYPOINT` nel Dockerfile?

 - `RUN`: esegue comandi al **build time** e modifica l’immagine.  
 - `CMD`: definisce il **comando di default** quando il container parte (ma può essere sovrascritto).  
- `ENTRYPOINT`: definisce il **processo principale** del container, solitamente non sovrascrivibile.

---

## 6. Come si mappa una porta tra host e container?

Con l’opzione `-p` del comando `docker run`, ad esempio:
> ```bash
> docker run -p 8080:80 nome-immagine
> ```
> Dove `8080` è la porta dell’host e `80` quella esposta dal container.

---

## 7. Come si persiste un dato oltre la vita del container?

Usando un **volume** Docker:
> ```bash
> docker run -v nome-volume:/percorso/container nome-immagine
> ```

 ==I volumi permettono di mantenere i dati anche dopo la rimozione del container==.

---

## 8. Come si collega un container a un database?

Attraverso una **rete Docker** o definendo le variabili di connessione (`DB_HOST`, `DB_USER`, ecc.).  
Ad esempio con `docker-compose`:
```yaml
 services:
  app:
    image: 
    myapp     
    depends_on:       
    - db
  db:
    image: postgres
```

---

## 9. Come si lista e gestisce i container attivi?

- Visualizzare container attivi:
```bash
 docker ps
```
- Fermare un container:
```bash 
 docker stop nome_container
```

 - Rimuovere un container:
  ```bash   
  docker rm 
  nome_container   
  ```

---

## 10. Cosa fa `docker-compose`?

È uno strumento che consente di definire e gestire **più container come un’unica applicazione**, tramite un file `docker-compose.yml`.  
 Esempio di comando:
```bash
 docker-compose up -d
```
> ==Avvia tutti i servizi definiti nel file in modalità detached.==

---
Anche se non serve essere esperti, mostrare di avere le basi fa una bella differenza. Ecco cosa sarebbe bene:

1. **Concetti fondamentali**
    
    - Pod, Deployment, ReplicaSet, StatefulSet, DaemonSet
        
    - Namespace, Service (ClusterIP, NodePort, LoadBalancer), Ingress
        
    - ConfigMap, Secret
        
2. **Architettura**
    
    - Componenti del cluster: master/control plane (apiserver, controller manager, scheduler), nodi/worker, kubelet, kube-proxy
        
    - Come funziona il networking tra i pod, tra pod e servizio
        
3. **Deployment**
    
    - Come si deploya un’applicazione su Kubernetes: definizione di YAML per Deployment/Service
        
    - Rolling updates vs rollback
        
4. **Scalabilità e affidabilità**
    
    - Replica, autoscaling (Horizontal Pod Autoscaler)
        
    - Tolleranza agli errori: cosa succede se un nodo cade
        
5. **Persistenza, archiviazione e volumi**
    
    - PersistentVolumes (PV) e PersistentVolumeClaims (PVC)
        
    - Tipi di volumi (storage locali vs remoti, networked storage)
        
6. **Configurazione e gestione**
    
    - ConfigMap + Secret per gestire configurazioni sensibili
        
    - Rollout di nuove versioni
        
7. **Monitoraggio, logging e sicurezza base**
    
    - Come puoi raccogliere log, metriche
        
    - Sicurezza base: RBAC, limiti di resource (CPU/memoria), limiti nei pod
        
8. **Strumenti / ecosistema**
    
    - `kubectl` comandi base
        
    - Minikube o kind per test locali
        
    - Conoscere almeno cosa sono Helm charts o manifeste simili (anche se non li conosci in dettaglio, sapere che esistono)
        
9. **Integrazione con CI/CD / DevOps workflow**
    
    - Deployment automatici (pipeline) verso Kubernetes
        
    - Gestione delle immagini (registry, tagging)
        
10. **Cloud provider / servizi gestiti**
    
    - Se l’azienda usa Kubernetes gestito (es: AKS su Azure, EKS su AWS, GKE su Google) → differenze rispetto al gestire un cluster “on-premise”
        

---

## 📋 Domande che potrebbero farti su Kubernetes

Ecco alcune possibili domande, pensate per livello junior, che possono uscire in un colloquio tecnico:

|#|Domanda|Cosa cercano|Follow‐up utile|
|---|---|---|---|
|1|**Cos’è un Pod in Kubernetes?**|Vogliono capire se sai un concetto base, cosa contiene, perché è il blocco fondamentale|“Che cosa succede se un Pod muore?”|
|2|**Qual è la differenza tra Deployment e ReplicaSet?**|Se sai come funziona il rollout e la gestione delle repliche|“Come fai rollback se una versione ha un bug?”|
|3|**Che tipi di Service conosci e quando usi ClusterIP vs NodePort vs LoadBalancer?**|Comprensione del networking interno/esterno del cluster|“Come configureresti un Ingress?”|
|4|**Spiegami cosa sono i ConfigMap e i Secret, e come li useresti in un progetto .NET**|Se sai gestire la configurazione, segreto, separazione configurazione vs codice|“Come eviti che un Secret venga esposto da un errore di configurazione?”|
|5|**Cos’è un PersistentVolume / PersistentVolumeClaim? Quando ne hai bisogno?**|Perseveranza dello storage, non effimero, relativamente avanzato ma utile|“Che tipo di storage useresti in cloud vs on-premise?”|
|6|**Cos’è l’Horizontal Pod Autoscaler (HPA)?**|Se sai come il cluster può scalare automaticamente|“Quali metriche può usare per scalare (CPU, memoria, custom metrics)?”|
|7|**Quali sono i comandi kubectl che usi più spesso?**|Capire se hai esperienza pratica, anche minima|“E come fai per vedere i log di un Pod?” “Come visualizzare lo stato di un Deployment?”|
|8|**Cosa succede quando fai rolling update di un Deployment?**|Capire se comprendi strategia di deployment, downtime minimo, rollback|“E se la nuova versione non si avvia?” “Come fai il rollback?”|
|9|**Che cosa sono Namespace in Kubernetes e a cosa servono?**|Organizzazione di cluster multi-team o multi-ambiente (dev/test/prod)|“Come isolare risorse tra namespace?”|
|10|**Hai mai usato Helm (o altri strumenti simili) per gestire release su Kubernetes?**|Anche se è bonus, se ne sai qualcosa dimostra impegno|“Se non lo usi, come gestiresti manifesti multipli, versioning, rollback?”|

---

## 🧭 Come rispondere bene

- Se non hai usato certe caratteristiche in produzione, va bene: **puoi dire che hai imparato/testato in locale** o “ho letto esempi / provato su cluster personale / con Minikube”.
    
- Porta esempi concreti: “Ho fatto un Deployment che usava XYZ”, “Ho sperimentato `kubectl logs`” o “ho montato un PVC per salvare i dati di Postgres su cluster dev”.
    
- Mostra curiosità: chiedere “quale provider Kubernetes usate qui?”, “usate un cluster gestito o self-managed?” → fa vedere che pensi al contesto aziendale.
    
- Se menzioni caching dei container image, versioning, rollback, limiti risorse → anche se basi, impressiona.
    

---

## 🔍 Cosa si fa nel cloud / Kubernetes (riferimento)

Alcune informazioni utili su come ci si muove con Kubernetes / containerizzazione:

- 'Azienda' offre **servizi di cloud consulting e advisory**, migrando apps verso piattaforme cloud e container-orchestrate come Kubernetes.
- Nei loro datasheet si parla di containerization come componente supportata per migrazioni cloud & ibridi
- Hanno progetti “cloud native” e DevOps/SRE come parte del loro modello operativo.
    
Quindi è plausibile che tu si debba parlare anche di come Kubernetes si inserisce in una pipeline di migrazione, automazione, gestione ibrida cloud/data center.

# Kubernetes – Guida di studio (livello Junior)

---

## Concetti fondamentali

### Pod
- È la **più piccola unità eseguibile** in Kubernetes.
- Contiene **uno o più container** che condividono:
  - lo **spazio di rete** (IP, porte),
  - il **filesystem temporaneo**,
  - e (opzionalmente) i volumi.
- Generalmente si usa **1 container per Pod**, ma più container se devono cooperare strettamente (es. sidecar pattern).

💬 *Follow-up:* “Cosa succede se un Pod muore?”  

> Kubernetes lo ricrea automaticamente tramite il **Deployment/ReplicaSet**.

---

### Deployment, ReplicaSet, StatefulSet, DaemonSet

#### Deployment
- Gestisce il ciclo di vita dei Pod e delle loro **repliche**.  
- Permette **rolling update** e **rollback** automatici.
- Crea e gestisce internamente un **ReplicaSet**.

#### ReplicaSet
- Mantiene un numero desiderato di Pod **identici** attivi.
- Non gestisce versioning o aggiornamenti: questo lo fa il Deployment.

#### StatefulSet
- Gestisce Pod con **identità persistente e ordinata** (es. database, servizi con stato).  
- I Pod hanno **nomi e volumi dedicati**.

#### DaemonSet
- Garantisce che **ci sia un Pod su ogni nodo** (es. log collector, monitoring agent).

---

### Namespace
- Servono per **isolare e organizzare risorse** all’interno del cluster.
- Permettono di separare ambienti (dev/test/prod) o team.

💬 *Follow-up:* “Come isoleresti risorse tra namespace?”  
> Con **limiti di risorsa**, **RBAC**, e **NetworkPolicy**.

---

### Service (ClusterIP, NodePort, LoadBalancer) + Ingress

#### Service
Permette ai Pod di comunicare **tra loro** e **verso l’esterno**:
- **ClusterIP** (default): accessibile solo all’interno del cluster.
- **NodePort**: espone il servizio su una porta del nodo host.
- **LoadBalancer**: crea un bilanciatore esterno (su cloud provider).

#### Ingress
- Gestisce il **routing HTTP/HTTPS** in ingresso.
- Può applicare regole di URL, certificati SSL, ecc.

💬 *Follow-up:* “Quando usi un Ingress?”  
> Quando vuoi un **punto d’ingresso unico** per più servizi HTTP.

---

### ConfigMap e Secret

- **ConfigMap:** memorizza configurazioni non sensibili (es. variabili d’ambiente, file di config).  
- **Secret:** memorizza informazioni sensibili (password, token, chiavi API), **base64 encoded**.  
- Entrambi possono essere montati come:
  - variabili d’ambiente (`envFrom`),
  - file (`volumeMounts`).

💬 *Follow-up:* “Come eviti che un Secret venga esposto?”  
> Usando RBAC, cifratura at-rest, e montandolo solo nei Pod che ne hanno bisogno.

---

## Architettura del cluster

### Componenti principali

#### Control Plane (Master)
- **kube-apiserver:** espone l’API REST, punto centrale del cluster.  
- **controller-manager:** monitora lo stato e applica cambiamenti.  
- **scheduler:** decide su quale nodo avviare i Pod.  
- **etcd:** database chiave-valore che contiene lo stato del cluster.

#### Nodi (Worker)
- **kubelet:** gestisce i Pod e comunica con il control plane.  
- **kube-proxy:** gestisce il routing di rete dei servizi.  
- **container runtime:** (es. containerd, Docker) esegue i container.

---

### Networking in breve
- Ogni Pod ha un **IP univoco** nel cluster.  
- La comunicazione tra Pod è **diretta** (flat network).  
- I Service forniscono **DNS e load balancing** interni.  
- Ingress espone verso l’esterno.

---

## Deployment

### Come si deploya un’applicazione
- Si definiscono i manifest YAML per:
  - **Deployment** (specifica immagine, repliche, risorse),
  - **Service** (esposizione di rete),
  - eventuali **ConfigMap/Secret**.
- Si applica con:
```bash
  kubectl apply -f deployment.yaml
```

## Rolling update vs rollback

- **Rolling update**: sostituisce gradualmente i Pod con la nuova versione.

- **Rollback**: ripristina automaticamente la versione precedente se fallisce.

💬 Follow-up: “Cosa succede se la nuova versione non parte?”
> Il Deployment rileva il fallimento e mantiene la vecchia.

## Scalabilità e affidabilità

### Replica

Ogni **ReplicaSet** mantiene **N Pod attivi**.
Se un Pod muore, ne viene creato uno nuovo.

### Autoscaling (HPA)

Aggiunge o rimuove Pod automaticamente in base a metriche (CPU, RAM, metriche custom).

```bash

kubectl autoscale deployment myapp --cpu-percent=80 --min=2 --max=10
```

### Tolleranza agli errori 

- Se un **nodo** cade, i <span style="color: #8392a4">Pod vengono riallocati su altri nodi dal scheduler</span>.
- I **Deployment** garantiscono che **il numero di repliche rimanga costante**.

### Persistenza e volumi

#### PersistentVolume (PV)

Risorsa che rappresenta uno storage fisico o virtuale.

#### PersistentVolumeClaim (PVC)

È la richiesta fatta da un Pod per usare uno storage.

> **Kubernetes** collega PVC → PV.

##### 💬 Follow-up: “Quando serve un PVC?”

> Quando un’app ha dati che devono sopravvivere al riavvio del Pod.

### Configurazione e gestione

**ConfigMap** e **Secret** per gestire configurazioni esterne.

<span style="color: #8392a4">kubectl rollout restart</span> o<span style="color: #8392a4">rollout undo</span> per gestire nuove versioni.

### Monitoraggio, logging e sicurezza base

#### Logging e metriche
kubectl logs pod-name per log singolo Pod.

Strumenti comuni: Prometheus, Grafana, Loki, Fluentd, ELK stack.

#### Sicurezza

- RBAC (Role-Based Access Control): controlla chi può fare cosa.
- Resource limits: definire resources.requests e resources.limits per CPU/memoria.
- NetworkPolicy: controlla il traffico tra Pod.

#### Strumenti ed ecosistema

- kubectl get / describe / logs / exec
- Minikube o Kind per ambienti locali.
- Helm per pacchettizzare e versionare applicazioni (chart = bundle di YAML + config).

#### CI/CD e DevOps

Pipeline automatizzate (es. GitHub Actions, Azure DevOps, Jenkins) che:

- Buildano l’immagine Docker.
- La pushano in un container registry (ACR, ECR, ecc.).
- Eseguono kubectl apply o helm upgrade per deploy.

#### Cloud provider (AKS, EKS, GKE)

- AKS (Azure Kubernetes Service): gestito da Azure, integrazione con AAD e Azure Monitor.
- EKS (AWS): integrazione con IAM, CloudWatch.
- GKE (Google): autoscaling e upgrade automatici.

- Differenze principali: livello di gestione e billing, ma il concetto base è lo stesso.

