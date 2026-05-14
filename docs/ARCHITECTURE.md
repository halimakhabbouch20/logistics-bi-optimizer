# Architecture Technique - LogisticsBI Optimizer

## 1. Vue d'ensemble de l'Architecture

### Stack Technique Complet

Le système est construit selon une architecture en couches avec microservices et Data Warehouse.

```
┌──────────────────────────────────────────────────────────────────┐
│                    USER INTERFACE LAYER                          │
├──────────────────────────────┬──────────────────────────────────┤
│  Web Dashboard (Vue.js 3)    │  Mobile App (Flutter)           │
│  • Real-time monitoring      │  • Delivery tracking            │
│  • BI Analytics              │  • Performance metrics          │
│  • Route optimization        │  • Notifications                │
└──────────────────────────────┴──────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│                    API GATEWAY LAYER                             │
│                    (Node.js + Express)                           │
├──────────────────────────────────────────────────────────────────┤
│  • Authentication (JWT)         • Request Logging                │
│  • Rate Limiting                • Error Handling                 │
│  • CORS Management              • API Versioning                 │
└────────────────────────────────────────────────��─────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│                  SERVICE LAYER (Microservices)                   │
├──────────────────────────────┬──────────────────────────────────┤
│  Delivery Service             │  Analytics Service              │
│  • Create orders              │  • KPI calculation              │
│  • Track deliveries           │  • Report generation            │
│  • Update status              │  • Data aggregation             │
├──────────────────────────────┼──────────────────────────────────┤
│  Route Optimization Service   │  Prediction Service             │
│  • Solve VRP                  │  • Delay prediction             │
│  • Cost calculation           │  • Demand forecasting           │
│  • Vehicle assignment         │  • ML model inference           │
├──────────────────────────────┼──────────────────────────────────┤
│  User Management Service      │  Notification Service           │
│  • Auth/Authorization         │  • SMS alerts                   │
│  • Role management            │  • Email notifications          │
│  • User profiles              │  • Push notifications           │
└──────────────────────────────┴──────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│                    DATA ACCESS LAYER                             │
├──────────────────────────────┬──────────────────────────────────┤
│  Repository Pattern           │  Query Builder                  │
│  ��� Order Repository           │  • Optimized queries            │
│  • Delivery Repository        │  • Index management             │
│  • Route Repository           │  • Query caching                │
└──────────────────────────────┴──────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│                  PERSISTENT DATA LAYER                           │
├───────────────────────┬───────────────┬───────────────────────┤
│  PostgreSQL           │  Redis        │  TimescaleDB          │
│  (Main DB)            │  (Cache)      │  (Time-series)        │
└───────────────────────┴───────────────┴───────────────────────┘
                            ↓
┌────────────────────────────────────────���─────────────────────────┐
│                  DATA WAREHOUSE & BI LAYER                       │
├──────────────────────────────┬──────────────────────────────────┤
│  Data Warehouse (DW Schema)   │  BI Platform                   │
│  • Fact tables                │  • Apache Superset             │
│  • Dimension tables           │  • Custom dashboards           │
│  • Aggregate tables           │  • OLAP cubes                  │
└──────────────────────────────┴──────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│            ETL & DATA PIPELINE LAYER                             │
│              (Apache Airflow)                                    │
├─────────────────────────────���────────────────────────────────────┤
│  • Extract: APIs, Databases, GPS trackers                        │
│  • Transform: Data cleaning, aggregation, enrichment             │
│  • Load: DW, Cache, Real-time streams                            │
│  • Scheduling: Daily, hourly, real-time pipelines                │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│            AI/ML & OPTIMIZATION LAYER                            │
├──────────────────────────────┬──────────────────────────────────┤
│  ML Models                    │  Optimization Engine            │
│  • Delay prediction           │  • Route optimization (VRP)     │
│  • Demand forecasting         │  • Cost minimization            │
│  • Anomaly detection          │  • Vehicle scheduling           │
│  • Classification             │  • Load balancing              │
└──────────────────────────────┴──────────────────────────────────┘
```

## 2. Détail des Composants

### 2.1 Backend API (Node.js + Express)

**Responsabilités:**
- Exposer des endpoints REST pour les applications frontend
- Gérer l'authentification et l'autorisation
- Orchestrer les services métier
- Gérer les connexions temps réel (WebSocket)
- Valider les données

**Structure:**
```
backend/
├── src/
│   ├── config/           # Configuration application
│   ├── controllers/      # Logique des endpoints
│   ├── models/           # Schémas DB (Sequelize/TypeORM)
│   ├── services/         # Logique métier
│   ├── repositories/     # Accès aux données
│   ├── middleware/       # Auth, validation, logging
│   ├── routes/           # Définition des routes
│   ├── utils/            # Utilitaires
│   └── app.js            # Configuration Express
├── tests/                # Tests unitaires et int��gration
├── Dockerfile
└── package.json
```

### 2.2 Frontend Web (Vue.js 3)

**Composants Clés:**
- Dashboard Principal (KPIs, Métriques)
- Suivi des Livraisons (Carte interactive)
- Gestion des Commandes
- Analyse des Routes
- Rapports BI
- Administration

**Technologies:**
- Vue 3 (Composition API)
- Vuex/Pinia - State management
- Leaflet - Cartes géospatiales
- D3.js - Visualisations avancées
- Chart.js - Graphiques

### 2.3 Frontend Mobile (Flutter)

**Écrans:**
- Authentification
- Tableau de bord chauffeur
- Détails de livraison
- Navigation GPS
- Historique des livraisons
- Notifications

### 2.4 Data Warehouse

**Schéma Dimensionnel:**

```sql
-- FACT TABLES
FACT_DELIVERIES (
    delivery_id,
    order_id,
    vehicle_id,
    driver_id,
    origin_location_id,
    destination_location_id,
    time_id,
    cost,
    distance_km,
    duration_minutes,
    actual_duration_minutes,
    delay_minutes,
    status
)

FACT_ORDERS (
    order_id,
    customer_id,
    location_id,
    time_id,
    amount,
    quantity,
    status
)

-- DIMENSION TABLES
DIM_TIME (
    time_id,
    date,
    year,
    month,
    day,
    week,
    day_of_week,
    hour,
    is_holiday
)

DIM_LOCATION (
    location_id,
    city,
    province,
    region,
    latitude,
    longitude,
    postal_code
)

DIM_VEHICLE (
    vehicle_id,
    vehicle_type,
    capacity,
    registration_year,
    fuel_type
)

DIM_CUSTOMER (
    customer_id,
    name,
    segment,
    location_id,
    subscription_type
)
```

### 2.5 ETL Pipeline (Apache Airflow)

**DAGs (Directed Acyclic Graphs):**

1. **daily_gps_pipeline** - Collecte données GPS temps réel
2. **orders_etl** - Synchronisation commandes
3. **deliveries_etl** - Historique livraisons
4. **weather_pipeline** - Données météo (API)
5. **dw_refresh** - Rafraîchissement DW
6. **model_training** - Réentraînement ML

### 2.6 BI Analytics (Apache Superset)

**Dashboards:**
- Executive Summary
- Operations Management
- Financial Analysis
- Driver Performance
- Customer Analytics
- Predictive Insights

## 3. Flux de Données

### 3.1 Flux Temps Réel (Real-time)

```
GPS Tracker (IoT) 
    ↓ (HTTP POST)
Node.js API (WebSocket)
    ↓ (Emit event)
Frontend Web/Mobile (Live Map)
    
Parallèlement:
Redis Cache
    ↓
TimescaleDB (Time-series)
    ↓ (Periodic flush)
PostgreSQL (Main DB)
```

### 3.2 Flux Batch (ETL Nightly)

```
Multiple Data Sources
├── Order System API
├── Vehicle Tracking DB
├── Weather API
└── Historical Logs
    ↓
Apache Airflow
├── Extract (SQL queries)
├── Transform (Python)
└── Load (PostgreSQL)
    ↓
Data Warehouse
    ↓
ML Models (Training)
    ↓
Superset (Refresh)
```

## 4. Patterns et Best Practices

### 4.1 Architecture Patterns

- **Microservices** : Services découplés pour scalabilité
- **Repository Pattern** : Abstraction de l'accès aux données
- **Service Layer** : Logique métier centralisée
- **Dependency Injection** : Gestion des dépendances
- **Event-Driven** : Communication asynchrone
- **CQRS** : Séparation lecture/écriture (optionnel)

### 4.2 Data Best Practices

- **Data Validation** : Validation à l'entrée
- **Referential Integrity** : Contraintes FK
- **Indexing Strategy** : Index sur colonnes fréquentes
- **Partitioning** : Partitionnement temporel DW
- **Data Quality** : Monitoring et alertes

### 4.3 Performance Optimization

- **Caching** : Redis pour données chaudes
- **Query Optimization** : EXPLAIN ANALYZE
- **Connection Pooling** : PgBouncer
- **Pagination** : Limit/Offset ou Cursor-based
- **Async Processing** : Jobs queues (Bull, Celery)

## 5. Sécurité

### 5.1 Authentification & Autorisation

- **JWT Tokens** : Stateless authentication
- **OAuth2** : Intégration tierce
- **Role-Based Access Control (RBAC)**
- **Multi-Factor Authentication (MFA)** : 2FA

### 5.2 Données Sensibles

- **Chiffrement** : TLS/SSL en transit, AES-256 au repos
- **Hachage Mot de Passe** : bcrypt/Argon2
- **Secrets Management** : HashiCorp Vault
- **GDPR Compliance** : Anonymisation, right to forget

### 5.3 API Security

- **Rate Limiting** : Token bucket algorithm
- **CORS** : Whitelist de domaines
- **CSRF Protection** : SameSite cookies
- **Input Validation** : Sanitization & validation
- **SQL Injection Prevention** : Parameterized queries

## 6. Scalabilité

### 6.1 Horizontal Scaling

- **Kubernetes** : Orchestration conteneurs
- **Load Balancing** : Nginx, HAProxy
- **Database Replication** : Read replicas
- **Caching Layer** : Redis cluster

### 6.2 Vertical Scaling

- **Resource Allocation** : CPU, RAM optimization
- **Database Tuning** : Query optimization
- **Code Optimization** : Profiling et benchmarking

## 7. Monitoring & Observability

### 7.1 Metrics

- **Prometheus** : Collection de métriques
- **Grafana** : Visualisation
- **Custom Metrics** : Business KPIs

### 7.2 Logging

- **ELK Stack** : Elasticsearch, Logstash, Kibana
- **Winston** : Application logging
- **Log Aggregation** : Centralized logging

### 7.3 Tracing

- **Jaeger** : Distributed tracing
- **Request Tracking** : Correlation IDs
- **Performance Profiling** : Bottleneck identification

## 8. Déploiement

Voir le document `DEPLOYMENT.md` pour les détails complets.

---

**Document créé:** 2026-05-14
**Version:** 1.0.0
