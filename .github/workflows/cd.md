# Continuous Deployment (CD) Dokumentation

## Inhaltsverzeichnis

1. [Übersicht](#übersicht)
2. [Gewählte Deployment-Methode](#gewählte-deployment-methode)
3. [Alternative Deployment-Methoden](#alternative-deployment-methoden)
4. [Umgebungen](#umgebungen)
5. [Branching-Strategie & Versionierung](#branching-strategie--versionierung)
6. [Konfiguration & Secrets Management](#konfiguration--secrets-management)
7. [Rollout-Strategien](#rollout-strategien)
8. [Rollback-Strategien](#rollback-strategien)
9. [Skalierbarkeit & Ausfallsicherheit](#skalierbarkeit--ausfallsicherheit)

---

## Übersicht

Diese Dokumentation beschreibt den Continuous Deployment Prozess für das M324 Ticketsystem-Projekt. Der CD-Prozess automatisiert das Erstellen von Releases (Delivery) und die Auslieferung auf Server (Deployment).

### Pipeline-Ablauf

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Format    │───►│   Linting   │───►│    Tests    │───►│   Deploy    │
│    Check    │    │   (ESLint)  │    │ Unit + Int. │    │  (Docker)   │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                             │
                                             ▼
                                      ┌─────────────┐
                                      │  Security   │
                                      │   Audit     │
                                      └─────────────┘
```

---

## Gewählte Deployment-Methode

### Docker Image Build & Push + Docker Compose

Wir haben uns für **Docker Image Build/Push zu Docker Hub** in Kombination mit **Docker Compose auf dem Produktionsserver** entschieden.

#### Warum diese Methode?

| Vorteil                  | Beschreibung                          |
| ------------------------ | ------------------------------------- |
| **Einfachheit**          | Leicht zu verstehen und zu warten     |
| **Portabilität**         | Container laufen überall gleich       |
| **Reproduzierbarkeit**   | Identische Images in allen Umgebungen |
| **Schnelles Deployment** | Pull & Up in Sekunden                 |
| **Kosteneffizienz**      | Keine komplexe Infrastruktur nötig    |

#### Deployment-Flow

```
GitHub Actions                    Docker Hub                    Prod Server
┌────────────┐                   ┌───────────┐                 ┌───────────┐
│ 1. Build   │──────────────────►│ 2. Store  │◄────────────────│ 3. Pull   │
│    Image   │     docker push   │   Image   │   docker pull   │   Image   │
└────────────┘                   └───────────┘                 └───────────┘
                                                                     │
                                                                     ▼
                                                               ┌───────────┐
                                                               │ 4. Run    │
                                                               │  Compose  │
                                                               └───────────┘
```

#### Pipeline-Konfiguration (cicd.yml)

```yaml
deploy:
  name: Build & Deploy
  runs-on: ubuntu-latest
  needs: [unit-tests, integration-tests, security-audit]
  if: github.ref == 'refs/heads/main' && github.event_name == 'push'

  steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Build Docker image
      run: docker build -t ${{ secrets.DOCKER_USER }}/${{ secrets.DOCKER_IMAGE }}:latest ./Code

    - name: Login to Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login --username "${{ secrets.DOCKER_USER }}" --password-stdin

    - name: Push Docker image
      run: docker push ${{ secrets.DOCKER_USER }}/${{ secrets.DOCKER_IMAGE }}:latest

    - name: Deploy to server
      uses: appleboy/ssh-action@v1
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USER }}
        key: ${{ secrets.SERVER_SSH_KEY }}
        script: |
          cd ${{ secrets.APP_PATH }}
          docker compose -f docker-compose.prod.yml down
          docker compose -f docker-compose.prod.yml pull
          docker compose -f docker-compose.prod.yml up -d
```

#### Docker Compose Production (docker-compose.prod.yml)

```yaml
services:
  app:
    image: abinayantbz/m324-app:latest
    ports:
      - "3000:3000"
    environment:
      - PORT=3000
      - NODE_ENV=production
      - MONGO_URI=mongodb://mongo:27017/app
      - HOST_URL=http://localhost:3000
    depends_on:
      mongo:
        condition: service_healthy
    restart: unless-stopped

  mongo:
    image: mongo:7
    volumes:
      - mongo_data:/data/db
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

volumes:
  mongo_data:
```

---

## Alternative Deployment-Methoden

### 1. Kubernetes (K8s)

**Beschreibung:** Container-Orchestrierung für grosse Anwendungen mit automatischer Skalierung.

```yaml
# Beispiel: kubernetes-deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ticketsystem
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ticketsystem
  template:
    metadata:
      labels:
        app: ticketsystem
    spec:
      containers:
        - name: app
          image: abinayantbz/m324-app:latest
          ports:
            - containerPort: 3000
          env:
            - name: NODE_ENV
              value: "production"
            - name: MONGO_URI
              valueFrom:
                secretKeyRef:
                  name: app-secrets
                  key: mongo-uri
---
apiVersion: v1
kind: Service
metadata:
  name: ticketsystem-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 3000
  selector:
    app: ticketsystem
```

| Vorteile        | Nachteile                    |
| --------------- | ---------------------------- |
| Auto-Scaling    | Komplexe Konfiguration       |
| Self-Healing    | Steile Lernkurve             |
| Load Balancing  | Overhead für kleine Projekte |
| Rolling Updates | Höhere Kosten                |

### 2. Docker Swarm

**Beschreibung:** Native Docker-Clustering-Lösung, einfacher als Kubernetes.

```yaml
# docker-compose.swarm.yml
version: "3.8"
services:
  app:
    image: abinayantbz/m324-app:latest
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
      rollback_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
    ports:
      - "3000:3000"
    networks:
      - app-network

networks:
  app-network:
    driver: overlay
```

**Deployment-Befehl:**

```bash
docker stack deploy -c docker-compose.swarm.yml ticketsystem
```

| Vorteile                  | Nachteile                |
| ------------------------- | ------------------------ |
| Einfacher als K8s         | Weniger Features als K8s |
| Native Docker-Integration | Kleinere Community       |
| Schneller Einstieg        | Limitierte Auto-Scaling  |

### 3. AWS Fargate (Serverless Container)

**Beschreibung:** Serverless Container-Ausführung ohne Server-Management.

```yaml
# Beispiel GitHub Actions für AWS Fargate
deploy-fargate:
  runs-on: ubuntu-latest
  steps:
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: eu-central-1

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v2

    - name: Build & Push to ECR
      run: |
        docker build -t $ECR_REGISTRY/m324-app:$IMAGE_TAG ./Code
        docker push $ECR_REGISTRY/m324-app:$IMAGE_TAG

    - name: Update ECS Service
      run: |
        aws ecs update-service \
          --cluster m324-cluster \
          --service ticketsystem \
          --force-new-deployment
```

| Vorteile                | Nachteile                    |
| ----------------------- | ---------------------------- |
| Kein Server-Management  | Vendor Lock-in               |
| Automatische Skalierung | Höhere Kosten bei hoher Last |
| Pay-per-Use             | Cold Starts möglich          |

### 4. Direktes Kompilieren auf Server

**Beschreibung:** Traditionelle Methode ohne Container.

```yaml
# Beispiel GitHub Actions
deploy-direct:
  runs-on: ubuntu-latest
  steps:
    - name: Deploy to server
      uses: appleboy/ssh-action@v1
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USER }}
        key: ${{ secrets.SERVER_SSH_KEY }}
        script: |
          cd /var/www/ticketsystem
          git pull origin main
          npm ci --production
          pm2 restart ticketsystem
```

| Vorteile                | Nachteile              |
| ----------------------- | ---------------------- |
| Kein Container-Overhead | Umgebungs-Unterschiede |
| Direkter Zugriff        | Manuelle Dependencies  |
| Einfaches Debugging     | Schwierige Rollbacks   |

### Vergleichsmatrix

| Methode            | Komplexität | Skalierbarkeit | Kosten      | Rollback  |
| ------------------ | ----------- | -------------- | ----------- | --------- |
| **Docker Compose** | Niedrig     | Niedrig        | Niedrig     | Mittel    |
| Kubernetes         | Hoch        | Sehr Hoch      | Mittel-Hoch | Einfach   |
| Docker Swarm       | Mittel      | Mittel         | Niedrig     | Einfach   |
| AWS Fargate        | Mittel      | Hoch           | Variabel    | Einfach   |
| Direkt             | Niedrig     | Niedrig        | Niedrig     | Schwierig |

---

## Umgebungen

### Definierte Umgebungen

```
┌────────────┐    ┌────────────┐    ┌────────────┐    ┌────────────┐
│    DEV     │───►│     QA     │───►│  STAGING   │───►│    PROD    │
│ (lokal)    │    │  (Test)    │    │ (Pre-Prod) │    │ (Live)     │
└────────────┘    └────────────┘    └────────────┘    └────────────┘
```

### 1. Development (DEV)

- **Zweck:** Lokale Entwicklung
- **Branch:** `feature/*`, `develop`
- **Deployment:** Manuell via `docker-compose up`
- **Datenbank:** Lokale MongoDB
- **URL:** `http://localhost:3000`

### 2. Quality Assurance (QA)

- **Zweck:** Automatisierte Tests, Code-Review
- **Branch:** `develop`, Pull Requests
- **Deployment:** Automatisch bei PR
- **Datenbank:** Ephemere Test-DB
- **URL:** -

### 3. Staging

- **Zweck:** Finale Tests vor Production
- **Branch:** `release/*`
- **Deployment:** Automatisch bei Release-Branch
- **Datenbank:** Kopie der Prod-Daten (anonymisiert)
- **URL:** -

### 4. Production (PROD)

- **Zweck:** Live-System für Endbenutzer
- **Branch:** `main`
- **Deployment:** Automatisch nach Merge in main
- **Datenbank:** Produktions-MongoDB
- **URL:** `https://m324.itsabi.com`

### Umgebungs-Konfiguration

```yaml
# .github/workflows/cicd.yml - Erweitert für Multi-Environment
deploy-staging:
  if: startsWith(github.ref, 'refs/heads/release/')
  # ... Staging Deployment

deploy-production:
  if: github.ref == 'refs/heads/main'
  # ... Production Deployment
```

---

## Branching-Strategie & Versionierung

### Git Flow

```
main ─────●─────────────●─────────────●───────►
           \           / \           /
            \         /   \         /
release/1.0  ●───────●     ●───────● release/1.1
              \     /       \     /
               \   /         \   /
develop ────●───●───●───●───●───●───●───●────►
            │       │       │       │
feature/A   ●───●───●       │       │
                            │       │
feature/B           ●───●───●       │
                                    │
hotfix/fix                  ●───────●
```

### Branch-zu-Umgebung Mapping

| Branch      | Umgebung       | Deployment          |
| ----------- | -------------- | ------------------- |
| `feature/*` | DEV            | Manuell             |
| `develop`   | QA             | Automatisch (Tests) |
| `release/*` | STAGING        | Automatisch         |
| `main`      | PRODUCTION     | Automatisch         |
| `hotfix/*`  | STAGING + PROD | Automatisch         |

### Versionierung (Semantic Versioning)

```
MAJOR.MINOR.PATCH
  │     │     │
  │     │     └── Bug Fixes (1.0.1)
  │     └──────── Neue Features (1.1.0)
  └────────────── Breaking Changes (2.0.0)
```

**Beispiel für Tagged Releases:**

```bash
# Release erstellen
git tag -a v1.2.0 -m "Release 1.2.0: Neue Ticket-Features"
git push origin v1.2.0
```

**Pipeline mit Versionierung:**

```yaml
- name: Build Docker image with version
  run: |
    VERSION=${GITHUB_REF#refs/tags/v}
    docker build -t ${{ secrets.DOCKER_USER }}/${{ secrets.DOCKER_IMAGE }}:$VERSION ./Code
    docker build -t ${{ secrets.DOCKER_USER }}/${{ secrets.DOCKER_IMAGE }}:latest ./Code
```

---

## Konfiguration & Secrets Management

### GitHub Secrets (Aktuell verwendet)

Alle sensiblen Daten werden als GitHub Secrets gespeichert:

| Secret            | Beschreibung                 |
| ----------------- | ---------------------------- |
| `DOCKER_USER`     | Docker Hub Benutzername      |
| `DOCKER_PASSWORD` | Docker Hub Passwort/Token    |
| `DOCKER_IMAGE`    | Name des Docker Images       |
| `SERVER_HOST`     | IP/Hostname des Prod-Servers |
| `SERVER_USER`     | SSH Benutzername             |
| `SERVER_SSH_KEY`  | Privater SSH-Schlüssel       |
| `APP_PATH`        | Pfad zur App auf dem Server  |

### Umgebungsvariablen

**Nicht-sensitive Konfiguration (in docker-compose):**

```yaml
environment:
  - PORT=3000
  - NODE_ENV=production
  - HOST_URL=http://localhost:3000
```

**Sensitive Konfiguration (via Secrets):**

```yaml
environment:
  - MONGO_URI=${MONGO_URI} # Aus .env Datei
  - JWT_SECRET=${JWT_SECRET}
  - API_KEY=${API_KEY}
```

### Best Practices für Sicherheit

1. **Keine Secrets im Code**

   ```bash
   # .gitignore
   .env
   .env.local
   .env.production
   *.pem
   *.key
   ```

2. **Secrets Rotation**
   - Docker Hub Tokens: Alle 90 Tage
   - SSH Keys: Jährlich
   - API Keys: Bei Bedarf

3. **Least Privilege Principle**
   - SSH-User nur mit notwendigen Rechten
   - Docker Hub: Deploy Token statt Passwort

4. **Environment-spezifische Secrets**
   ```yaml
   # GitHub Environment Secrets
   environments:
     staging:
       secrets: [STAGING_DB_URI, ...]
     production:
       secrets: [PROD_DB_URI, ...]
   ```

---

## Rollout-Strategien

### 1. Rolling Update (Aktuell implementiert)

```
Zeit →
Server 1: [v1.0] ──► [v1.1] ────────────────►
Server 2: [v1.0] ────────► [v1.1] ──────────►
Server 3: [v1.0] ──────────────► [v1.1] ────►
```

**Vorteile:**

- Zero Downtime
- Schrittweise Aktualisierung
- Einfache Implementierung

### 2. Blue-Green Deployment

```
          ┌─────────────┐
          │ Load        │
          │ Balancer    │
          └──────┬──────┘
                 │
        ┌────────┴────────┐
        ▼                 ▼
┌───────────────┐ ┌───────────────┐
│  BLUE (v1.0)  │ │ GREEN (v1.1)  │
│   [ACTIVE]    │ │   [STANDBY]   │
└───────────────┘ └───────────────┘
```

**Implementierung:**

```yaml
deploy-blue-green:
  steps:
    - name: Deploy to Green
      run: |
        ssh $SERVER "
          docker compose -f docker-compose.green.yml up -d
          # Health Check
          curl -f http://localhost:3001/health || exit 1
        "

    - name: Switch Traffic
      run: |
        ssh $SERVER "
          # Update nginx/Load Balancer
          ln -sf /etc/nginx/green.conf /etc/nginx/active.conf
          nginx -s reload
        "

    - name: Stop Blue
      run: |
        ssh $SERVER "docker compose -f docker-compose.blue.yml down"
```

### 3. Canary Deployment

```
100% Traffic
     │
     ▼
┌─────────────────────────────────────┐
│           v1.0 (Stable)             │
└─────────────────────────────────────┘

Nach Canary:
90% Traffic              10% Traffic
     │                        │
     ▼                        ▼
┌────────────────────┐ ┌──────────────┐
│    v1.0 (Stable)   │ │ v1.1 (Canary)│
└────────────────────┘ └──────────────┘
```

**Implementierung mit Nginx:**

```nginx
upstream backend {
    server app-stable:3000 weight=9;
    server app-canary:3000 weight=1;
}
```

### Vergleich der Rollout-Strategien

| Strategie  | Risiko       | Ressourcen | Rollback-Zeit |
| ---------- | ------------ | ---------- | ------------- |
| Rolling    | Mittel       | 1x         | Mittel        |
| Blue-Green | Niedrig      | 2x         | Sofort        |
| Canary     | Sehr Niedrig | 1.1x       | Sofort        |

---

## Rollback-Strategien

### 1. Automatisches Rollback (Health-Check basiert)

```yaml
deploy:
  steps:
    - name: Deploy new version
      run: |
        docker compose -f docker-compose.prod.yml up -d

    - name: Health Check
      run: |
        for i in {1..30}; do
          if curl -f http://localhost:3000/health; then
            echo "Health check passed"
            exit 0
          fi
          sleep 2
        done
        echo "Health check failed - Rolling back"
        exit 1

    - name: Rollback on failure
      if: failure()
      run: |
        docker compose -f docker-compose.prod.yml down
        docker compose -f docker-compose.prod.yml up -d --pull=never
        # Verwendet das vorherige lokale Image
```

### 2. Manuelles Rollback (Version-basiert)

```bash
# Rollback zu spezifischer Version
docker pull abinayantbz/m324-app:v1.2.0
docker compose -f docker-compose.prod.yml down
docker compose -f docker-compose.prod.yml up -d

# Oder via Tag
docker tag abinayantbz/m324-app:v1.2.0 abinayantbz/m324-app:latest
docker compose -f docker-compose.prod.yml up -d
```

### 3. Rollback-Checkliste

```
┌─────────────────────────────────────────────────────────┐
│                    ROLLBACK PROCEDURE                   │
├─────────────────────────────────────────────────────────┤
│ □ 1. Problem identifizieren und dokumentieren           │
│ □ 2. Team benachrichtigen                               │
│ □ 3. Letzte stabile Version ermitteln                   │
│ □ 4. Rollback ausführen                                 │
│ □ 5. Health-Checks verifizieren                         │
│ □ 6. Monitoring prüfen                                  │
│ □ 7. Stakeholder informieren                            │
│ □ 8. Post-Mortem planen                                 │
└─────────────────────────────────────────────────────────┘
```

### 4. Datenbank-Rollback Strategie

```bash
# Vor jedem Deployment: Backup erstellen
docker exec mongo mongodump --out /backup/$(date +%Y%m%d_%H%M%S)

# Bei Rollback: Restore
docker exec mongo mongorestore /backup/20240115_120000
```

---

## Skalierbarkeit & Ausfallsicherheit

### Horizontale Skalierung

**Mit Docker Compose:**

```yaml
services:
  app:
    image: abinayantbz/m324-app:latest
    deploy:
      replicas: 3
```

**Mit Load Balancer (Nginx):**

```nginx
upstream ticketsystem {
    least_conn;
    server app1:3000;
    server app2:3000;
    server app3:3000;
}

server {
    listen 80;
    location / {
        proxy_pass http://ticketsystem;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Architektur für hohe Verfügbarkeit

```
                    ┌─────────────────┐
                    │   CloudFlare    │
                    │   (CDN/DDoS)    │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  Load Balancer  │
                    │    (Nginx)      │
                    └────────┬────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
    ┌──────▼──────┐   ┌──────▼──────┐   ┌──────▼──────┐
    │   App #1    │   │   App #2    │   │   App #3    │
    │  (Node.js)  │   │  (Node.js)  │   │  (Node.js)  │
    └──────┬──────┘   └──────┬──────┘   └──────┬──────┘
           │                 │                 │
           └─────────────────┼─────────────────┘
                             │
                    ┌────────▼────────┐
                    │ MongoDB Replica │
                    │      Set        │
                    └─────────────────┘
```

### Health Checks

```yaml
# docker-compose.prod.yml erweitert
services:
  app:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Monitoring & Alerting

Integration mit Prometheus/Grafana für:

- CPU/Memory Auslastung
- Request Latenz
- Error Rates
- Container Health

---

## Zusammenfassung

| Aspekt                 | Implementierung                |
| ---------------------- | ------------------------------ |
| **Deployment-Methode** | Docker Image + Docker Compose  |
| **Registry**           | Docker Hub                     |
| **Orchestrierung**     | Docker Compose (Prod)          |
| **Environments**       | Dev, QA, Staging, Production   |
| **Branching**          | Git Flow                       |
| **Versionierung**      | Semantic Versioning            |
| **Secrets**            | GitHub Secrets                 |
| **Rollout**            | Rolling Update                 |
| **Rollback**           | Version-basiert + Health-Check |
| **Skalierung**         | Horizontal via Replicas        |

---

## Referenzen

- [Docker Compose Dokumentation](https://docs.docker.com/compose/)
- [GitHub Actions](https://docs.github.com/en/actions)
- [Semantic Versioning](https://semver.org/)
- [12-Factor App](https://12factor.net/)
