# Guide de Déploiement - LogisticsBI Optimizer

## 📋 Table des matières

1. [Prérequis](#prérequis)
2. [Installation Locale (Développement)](#installation-locale)
3. [Déploiement avec Docker](#déploiement-avec-docker)
4. [Déploiement Kubernetes](#déploiement-kubernetes)
5. [Configuration Production](#configuration-production)
6. [Monitoring et Maintenance](#monitoring-et-maintenance)

---

## ✅ Prérequis

### Matériel Recommandé
- **CPU:** 4 cores minimum (8+ recommandé)
- **RAM:** 16 GB minimum (32 GB recommandé)
- **Stockage:** 100 GB SSD
- **Bande passante:** 10 Mbps minimum

### Logiciels Requis

```bash
# Système d'exploitation
- Ubuntu 20.04 LTS ou supérieur
- OU Docker Desktop (Mac/Windows)

# Outils essentiels
- Docker >= 20.10
- Docker Compose >= 1.29
- Git >= 2.30
- Node.js >= 18
- Python >= 3.9
- PostgreSQL >= 13 (si pas de Docker)
```

### Vérifier l'installation

```bash
docker --version
docker-compose --version
node --version
python --version
git --version
```

---

## 🏠 Installation Locale (Développement)

### 1. Cloner le repository

```bash
git clone https://github.com/halimakhabbouch20/logistics-bi-optimizer.git
cd logistics-bi-optimizer
```

### 2. Configuration d'environnement

```bash
# Copier le fichier d'exemple
cp backend/.env.example backend/.env
cp frontend-web/.env.example frontend-web/.env
cp data-warehouse/.env.example data-warehouse/.env

# Éditer les variables
nano backend/.env
```

**backend/.env:**
```env
# Application
NODE_ENV=development
PORT=3000
HOST=0.0.0.0

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=logistics_dev
DB_USER=postgres
DB_PASSWORD=postgres_dev_password

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=your_jwt_secret_key_here_min_32_chars
JWT_EXPIRY=24h

# APIs
GOOGLE_MAPS_API_KEY=your_google_maps_key
OPENWEATHER_API_KEY=your_openweather_key

# CORS
CORS_ORIGIN=http://localhost:8080
```

**frontend-web/.env:**
```env
VUE_APP_API_URL=http://localhost:3000
VUE_APP_MAPS_API_KEY=your_google_maps_key
VUE_APP_ENVIRONMENT=development
```

### 3. Installation des dépendances

```bash
# Backend
cd backend
npm install
cd ..

# Frontend Web
cd frontend-web
npm install
cd ..

# Data Warehouse
cd data-warehouse
pip install -r requirements.txt
cd ..
```

### 4. Initialiser la base de données

```bash
# Créer la base de données
sudo -u postgres createdb logistics_dev

# Appliquer les migrations
cd backend
npm run migrate
cd ..

# Insérer les données de test
cd data-warehouse
python scripts/seed_data.py
cd ..
```

### 5. Démarrer les services

```bash
# Terminal 1 - Backend
cd backend
npm run dev

# Terminal 2 - Frontend Web
cd frontend-web
npm run serve

# Terminal 3 - Data Warehouse
cd data-warehouse
python -m airflow webserver

# Terminal 4 - Scheduler Airflow
cd data-warehouse
python -m airflow scheduler
```

### 6. Accéder aux applications

```
- Web App: http://localhost:8080
- API: http://localhost:3000
- API Docs: http://localhost:3000/api-docs
- Airflow: http://localhost:8081
```

---

## 🐳 Déploiement avec Docker

### 1. Structure Docker Compose

```bash
cd deployment
```

### 2. Configuration des images

**Fichier: docker-compose.yml**

```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:14-alpine
    container_name: logistics-postgres
    environment:
      POSTGRES_USER: ${DB_USER:-postgres}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-postgres}
      POSTGRES_DB: ${DB_NAME:-logistics_db}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/create_schema.sql:/docker-entrypoint-initdb.d/01-schema.sql
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - logistics-network

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: logistics-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - logistics-network

  # Backend API
  backend:
    build:
      context: ../backend
      dockerfile: Dockerfile
    container_name: logistics-backend
    environment:
      NODE_ENV: ${NODE_ENV:-production}
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: ${DB_NAME:-logistics_db}
      DB_USER: ${DB_USER:-postgres}
      DB_PASSWORD: ${DB_PASSWORD:-postgres}
      REDIS_HOST: redis
      REDIS_PORT: 6379
      JWT_SECRET: ${JWT_SECRET}
      PORT: 3000
    ports:
      - "3000:3000"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - ../backend/src:/app/src
      - ../backend/node_modules:/app/node_modules
    networks:
      - logistics-network
    restart: unless-stopped

  # Frontend Web
  frontend:
    build:
      context: ../frontend-web
      dockerfile: Dockerfile
    container_name: logistics-frontend
    environment:
      VUE_APP_API_URL: http://backend:3000
      VUE_APP_ENVIRONMENT: ${ENVIRONMENT:-production}
    ports:
      - "8080:80"
    depends_on:
      - backend
    networks:
      - logistics-network
    restart: unless-stopped

  # Apache Superset BI
  superset:
    image: apache/superset:latest
    container_name: logistics-superset
    environment:
      SUPERSET_SECRET_KEY: ${SUPERSET_SECRET_KEY}
      SQLALCHEMY_DATABASE_URI: postgresql://${DB_USER}:${DB_PASSWORD}@postgres:5432/superset_db
    ports:
      - "8088:8088"
    depends_on:
      - postgres
    volumes:
      - superset_data:/var/lib/superset
    networks:
      - logistics-network
    restart: unless-stopped

  # Apache Airflow
  airflow:
    image: apache/airflow:2.5.0-python3.9
    container_name: logistics-airflow
    environment:
      AIRFLOW_HOME: /opt/airflow
      AIRFLOW__CORE__LOAD_EXAMPLES: 'False'
      AIRFLOW__CORE__DAGS_FOLDER: /opt/airflow/dags
      AIRFLOW__DATABASE__SQLALCHEMY_CONN: postgresql://${DB_USER}:${DB_PASSWORD}@postgres:5432/airflow_db
    ports:
      - "8081:8080"
    depends_on:
      - postgres
    volumes:
      - ../data-warehouse/dags:/opt/airflow/dags
      - airflow_logs:/opt/airflow/logs
    networks:
      - logistics-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:
  superset_data:
  airflow_logs:

networks:
  logistics-network:
    driver: bridge
```

### 3. Démarrer les services Docker

```bash
# Créer un fichier .env
cat > .env << EOF
NODE_ENV=production
DB_USER=postgres
DB_PASSWORD=secure_password_123
DB_NAME=logistics_db
JWT_SECRET=your_jwt_secret_key_min_32_chars
SUPERSET_SECRET_KEY=your_superset_secret_key
ENVIRONMENT=production
EOF

# Démarrer tous les services
docker-compose up -d

# Vérifier l'état
docker-compose ps

# Voir les logs
docker-compose logs -f backend
```

### 4. Initialiser les bases de données

```bash
# Créer la base Superset
docker-compose exec superset superset db upgrade
docker-compose exec superset superset init

# Créer la base Airflow
docker-compose exec airflow airflow db init
docker-compose exec airflow airflow users create \
  --username admin \
  --password admin \
  --firstname Admin \
  --lastname User \
  --role Admin \
  --email admin@example.com
```

### 5. Accéder aux applications

```
- Web App: http://localhost:8080
- API: http://localhost:3000
- Superset: http://localhost:8088 (admin/admin)
- Airflow: http://localhost:8081 (admin/admin)
```

---

## ☸️ Déploiement Kubernetes

### 1. Installation de kubectl

```bash
# macOS
brew install kubectl

# Linux
sudo apt-get install -y kubectl

# Windows
choco install kubernetes-cli
```

### 2. Créer un cluster local (Minikube)

```bash
# Installer Minikube
brew install minikube

# Démarrer le cluster
minikube start --cpus=4 --memory=8192

# Vérifier l'état
kubectl cluster-info
kubectl get nodes
```

### 3. Créer les manifests Kubernetes

**File: deployment/kubernetes/namespace.yaml**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: logistics-bi
```

**File: deployment/kubernetes/postgres-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: logistics-bi
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:14-alpine
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: postgres-data
          mountPath: /var/lib/postgresql/data
      volumes:
      - name: postgres-data
        persistentVolumeClaim:
          claimName: postgres-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: logistics-bi
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
  type: ClusterIP
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
  namespace: logistics-bi
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

**File: deployment/kubernetes/backend-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: logistics-bi
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: logistics-backend:latest
        env:
        - name: DB_HOST
          value: postgres
        - name: DB_PORT
          value: "5432"
        - name: DB_NAME
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: db_name
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        ports:
        - containerPort: 3000
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: logistics-bi
spec:
  selector:
    app: backend
  ports:
  - port: 3000
    targetPort: 3000
  type: LoadBalancer
```

### 4. Déployer sur Kubernetes

```bash
# Créer le namespace
kubectl apply -f deployment/kubernetes/namespace.yaml

# Créer les secrets
kubectl create secret generic postgres-secret \
  --from-literal=password=secure_password \
  -n logistics-bi

kubectl create secret generic db-secret \
  --from-literal=password=secure_password \
  -n logistics-bi

# Créer ConfigMap
kubectl create configmap app-config \
  --from-literal=db_name=logistics_db \
  -n logistics-bi

# Déployer les services
kubectl apply -f deployment/kubernetes/postgres-deployment.yaml
kubectl apply -f deployment/kubernetes/backend-deployment.yaml

# Vérifier le déploiement
kubectl get pods -n logistics-bi
kubectl get svc -n logistics-bi
```

### 5. Accéder aux services

```bash
# Obtenir l'adresse IP du LoadBalancer
kubectl get svc backend -n logistics-bi

# Port-forward pour accès local
kubectl port-forward svc/backend 3000:3000 -n logistics-bi
```

---

## 🔐 Configuration Production

### 1. Variables d'environnement sécurisées

```bash
# Générer des clés sécurisées
openssl rand -base64 32  # JWT_SECRET
openssl rand -base64 32  # DB_PASSWORD

# Stocker dans un gestionnaire de secrets
# AWS Secrets Manager, HashiCorp Vault, etc.
```

### 2. Certificats SSL/TLS

```bash
# Générer un certificat auto-signé (test)
openssl req -x509 -newkey rsa:4096 \
  -keyout key.pem -out cert.pem -days 365 -nodes

# Utiliser Let's Encrypt en production
sh deployment/scripts/certbot-setup.sh
```

### 3. Configuration Nginx (Reverse Proxy)

**File: deployment/nginx.conf**

```nginx
upstream backend {
  server backend:3000;
}

upstream superset {
  server superset:8088;
}

server {
  listen 80;
  server_name logistics-bi.example.com;
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl http2;
  server_name logistics-bi.example.com;

  ssl_certificate /etc/ssl/certs/cert.pem;
  ssl_certificate_key /etc/ssl/private/key.pem;

  # API
  location /api {
    proxy_pass http://backend;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  # BI
  location /bi {
    proxy_pass http://superset/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  # Frontend
  location / {
    root /usr/share/nginx/html;
    try_files $uri $uri/ /index.html;
  }
}
```

### 4. Backups automatiques

```bash
#!/bin/bash
# deployment/scripts/backup.sh

BACKUP_DIR="/backups/logistics-bi"
DATE=$(date +%Y%m%d_%H%M%S)

# Backup PostgreSQL
docker-compose exec -T postgres pg_dump \
  -U postgres logistics_db > $BACKUP_DIR/db_$DATE.sql

# Compress
gzip $BACKUP_DIR/db_$DATE.sql

# Upload to S3
aws s3 cp $BACKUP_DIR/db_$DATE.sql.gz s3://logistics-backups/

# Cleanup old backups (7 jours)
find $BACKUP_DIR -name "db_*.sql.gz" -mtime +7 -delete
```

### 5. Monitoring et Alertes

**File: deployment/prometheus.yml**

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'backend'
    static_configs:
      - targets: ['localhost:3000']
  
  - job_name: 'postgres'
    static_configs:
      - targets: ['localhost:9187']
  
  - job_name: 'redis'
    static_configs:
      - targets: ['localhost:9121']

alert_rules:
  - alert: HighErrorRate
    expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
    for: 5m
    annotations:
      summary: "High error rate detected"
```

---

## 🔧 Monitoring et Maintenance

### Logs

```bash
# Docker Compose
docker-compose logs -f backend
docker-compose logs -f postgres

# Kubernetes
kubectl logs deployment/backend -n logistics-bi -f
kubectl logs pod/postgres-xxxxx -n logistics-bi -f
```

### Health Checks

```bash
# API Health
curl http://localhost:3000/health

# Database Connection
docker-compose exec postgres pg_isready -U postgres

# Redis Connection
docker-compose exec redis redis-cli ping
```

### Database Maintenance

```bash
# Backup
docker-compose exec postgres pg_dump \
  -U postgres logistics_db > backup.sql

# Restore
docker-compose exec -T postgres psql \
  -U postgres logistics_db < backup.sql

# Vacuum (Optimize)
docker-compose exec postgres psql \
  -U postgres -c "VACUUM ANALYZE;"
```

### Scale Services

```bash
# Kubernetes
kubectl scale deployment backend --replicas=5 -n logistics-bi

# Docker Compose (manual)
docker-compose up -d --scale backend=3
```

---

## 📊 Performance Tuning

### PostgreSQL

```sql
-- Augmenter shared_buffers
ALTER SYSTEM SET shared_buffers = '256MB';

-- Augmenter work_mem
ALTER SYSTEM SET work_mem = '16MB';

-- Activer le parallélisme
ALTER SYSTEM SET max_parallel_workers = 4;

-- Reload
SELECT pg_reload_conf();
```

### Redis

```bash
# Increase maxmemory
CONFIG SET maxmemory 2gb
CONFIG SET maxmemory-policy allkeys-lru
CONFIG REWRITE
```

---

## ✅ Checklist de Déploiement Production

- [ ] Variables d'environnement configurées
- [ ] Certificats SSL/TLS valides
- [ ] Backups automatiques actifs
- [ ] Monitoring et alertes configurés
- [ ] Base de données optimisée
- [ ] Cache Redis actif
- [ ] Load balancer configuré
- [ ] Health checks en place
- [ ] Documentation mise à jour
- [ ] Tests de charge réussis

---

**Dernière mise à jour:** 2026-05-14
**Version:** 1.0.0
