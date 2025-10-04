
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

Esempio yaml

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

- Docker per build & run.
- Kubernetes per deploy & scalabilità.

#### 5. Best Practice

- Scrivere Dockerfile ottimizzati (layer minimi, immagini leggere).
- Usare variabili d’ambiente/config esterne → niente credenziali hardcoded.
- Log e metriche centralizzate.
- Security: non girare come root dentro container.
- CI/CD: integrare Docker/K8s nei pipeline (es. Azure DevOps, GitHub Actions).
- Monitoraggio con Prometheus, Grafana, ELK/EFK.