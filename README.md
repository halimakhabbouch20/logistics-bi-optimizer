# 🚚 LogisticsBI Optimizer

**Système décisionnel intelligent pour l'optimisation logistique marocaine**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-16%2B-brightgreen)](https://nodejs.org/)
[![Vue.js](https://img.shields.io/badge/Vue.js-3-brightgreen)](https://vuejs.org/)
[![Python](https://img.shields.io/badge/Python-3.9%2B-blue)](https://www.python.org/)

## 📋 Table des matières

- [Vue d'ensemble](#vue-densemble)
- [Architecture](#architecture)
- [Fonctionnalités](#fonctionnalités)
- [Technologies](#technologies)
- [Installation](#installation)
- [Déploiement](#déploiement)
- [Documentation](#documentation)
- [Contribution](#contribution)

---

## 🎯 Vue d'ensemble

**LogisticsBI Optimizer** est une plateforme SaaS intelligente conçue pour optimiser l'ensemble de la chaîne logistique marocaine. Elle combine :

- 📍 **Suivi temps réel** des livraisons via GPS
- 📊 **Dashboard BI avancé** avec KPIs en direct
- 🤖 **Intelligence Artificielle** pour prédiction des retards et optimisation d'itinéraires
- 📱 **Application mobile** pour les chauffeurs
- 🔗 **Blockchain optionnelle** pour la traçabilité
- 🚀 **Microservices scalables** basés sur Docker

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   CLIENT LAYER                           │
├──────────────────────┬──────────────────────┬────────────┤
│  Vue.js Dashboard    │  Flutter Mobile App  │  Admin Web │
└──────────────────────┴──────────────────────┴────────────┘
                           ↓ HTTPS/WebSocket
┌─────────────────────────────────────────────────────────┐
│                   API LAYER (Node.js/Express)           │
├──────────────────────────────────────────────────────────┤
│  Authentication │ Tracking │ Analytics │ Predictions    │
└──────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│            DATA & BUSINESS LOGIC LAYER                  │
├────────────────────────────────────────────────────────┤
│  Services │ AI Engine │ Route Optimizer │ Reports       │
└────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│              DATABASE & CACHE LAYER                      │
├──────────────────────┬──────────────────────────────────┤
│  PostgreSQL          │  Redis (Cache & Real-time)       │
└──────────────────────┴──────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│           DATA WAREHOUSE & ANALYTICS                    │
├────────────┬─────────────────┬──────────────────────────┤
│ Apache     │  Data Pipeline  │  Apache Superset BI      │
│ Airflow    │  (ETL)          │  Dashboards              │
└────────────┴─────────────────┴──────────────────────────┘
```

### 📁 Structure des dossiers

```
logistics-bi-optimizer/
│
├── backend/                          # API Node.js/Express
│   ├── src/
│   │   ├── routes/                  # Définition des routes
│   │   ├── controllers/              # Logique métier
│   │   ├── services/                 # Services métier
│   │   ├── models/                   # Modèles Sequelize
│   │   ├── middleware/               # Middlewares
│   │   ├── utils/                    # Utilitaires
│   │   ├── config/                   # Configuration
│   │   └── app.js                    # Point d'entrée
│   ├── migrations/                   # Migrations DB
│   ├── seeders/                      # Données test
│   ├── package.json
│   └── Dockerfile
│
├── frontend-web/                     # Dashboard Vue.js
│   ├── src/
│   │   ├── components/               # Composants Vue
│   │   ├── pages/                    # Pages
│   │   ├── stores/                   # Pinia stores
│   │   ├── services/                 # Services API
│   │   ├── assets/                   # Images, CSS
│   │   ├── router/                   # Configuration routes
│   │   └── App.vue
│   ├── package.json
│   └── Dockerfile
│
├── frontend-mobile/                  # Application Flutter
│   ├── lib/
│   │   ├── screens/
│   │   ├── widgets/
│   │   ├── models/
│   │   ├── services/
│   │   └── main.dart
│   └── pubspec.yaml
│
├── ai-engine/                        # Modèles IA Python
│   ├── models/
│   │   ├── delay_predictor.py       # Prédiction retards
│   │   ├── route_optimizer.py       # Optimisation itinéraires
│   │   └── cost_analyzer.py         # Analyse coûts
│   ├── datasets/                     # Données d'entraînement
│   ├── scripts/
│   │   ├── train.py
│   │   ├── predict.py
│   │   └── evaluate.py
│   ├── api/                          # API FastAPI pour inférence
│   ├── requirements.txt
│   └── Dockerfile
│
├── data-warehouse/                   # Pipeline ETL & DW
│   ├── dags/                         # DAGs Apache Airflow
│   ├── sql/
│   │   ├── schema/                   # Schéma DW
│   │   ├── transformations/          # Requêtes SQL
│   │   └── seeds/                    # Données initiales
│   ├── python/                       # Scripts Python ETL
│   └── docker-compose.yml
│
├── bi-analytics/                     # Dashboards BI Superset
│   ├── dashboards/                   # Config dashboards JSON
│   ├── datasources/                  # Config sources données
│   └── sql_uploads/                  # Requêtes personnalisées
│
├── blockchain/                       # Smart Contracts Ethereum
│   ├── contracts/
│   │   └── Traceability.sol
│   ├── migrations/
│   ├── test/
│   ├── truffle-config.js
│   └── package.json
│
├── deployment/                       # Configuration déploiement
│   ├── kubernetes/
│   │   ├── backend-deployment.yaml
│   │   ├── frontend-deployment.yaml
│   │   └── services.yaml
│   ├── docker-compose.yml
│   └── nginx.conf
│
├── docs/                             # Documentation
│   ├── ARCHITECTURE.md
│   ├── API.md
│   ├── DATABASE.md
│   ├── AI_MODELS.md
│   ├── DEPLOYMENT.md
│   ├── CONTRIBUTING.md
│   └── diagrams/
│
├── .gitignore
├── LICENSE
└── docker-compose.yml               # Orchestration complète
```

---

## ⚙️ Technologies

### Backend
- **Node.js 16+** avec Express.js
- **PostgreSQL** pour les données relationnelles
- **Redis** pour le cache et real-time
- **Socket.io** pour les WebSockets
- **JWT** pour l'authentification
- **Sequelize** comme ORM

### Frontend Web
- **Vue.js 3** Composition API
- **Pinia** pour le state management
- **Vue Router** pour la navigation
- **Axios** pour les requêtes HTTP
- **TailwindCSS** pour le styling
- **D3.js** et **Chart.js** pour les visualisations
- **Leaflet** pour les cartes
- **Vite** comme bundler

### Frontend Mobile
- **Flutter** (Dart)
- **Provider** pour la gestion d'état
- **GetIt** pour l'injection de dépendances
- **http** pour les requêtes

### AI & Data
- **Python 3.9+**
- **TensorFlow/Keras** pour le deep learning
- **Scikit-learn** pour ML classique
- **Optuna** pour l'optimisation hyperparamètres
- **Apache Airflow** pour l'orchestration ETL
- **Apache Superset** pour les dashboards BI

### Blockchain
- **Ethereum** et **Solidity**
- **Truffle** pour le développement
- **Web3.js** pour l'intégration

### DevOps
- **Docker** et **Docker Compose**
- **Kubernetes** (optionnel)
- **GitHub Actions** pour CI/CD
- **Nginx** comme reverse proxy

---

## 🚀 Installation

### Prérequis
- Docker & Docker Compose
- Node.js 16+
- Python 3.9+
- PostgreSQL 13+
- Redis 6+

### Démarrage rapide

```bash
# 1. Cloner le repo
git clone https://github.com/halimakhabbouch20/logistics-bi-optimizer.git
cd logistics-bi-optimizer

# 2. Démarrer tous les services
docker-compose up -d

# 3. Vérifier les services
docker-compose ps

# 4. Accéder aux applications
# Frontend Web: http://localhost:3000
# API Backend: http://localhost:5000
# Superset BI: http://localhost:8088
# Airflow: http://localhost:8080
```

### Installation manuelle (sans Docker)

```bash
# Backend
cd backend
npm install
npm run migrate
npm run seed
npm start

# Frontend Web
cd frontend-web
npm install
npm run dev

# AI Engine
cd ai-engine
pip install -r requirements.txt
python api/main.py
```

---

## 🔑 Fonctionnalités principales

### 📊 Dashboard BI
- ✅ KPIs en temps réel
- ✅ Graphiques interactifs
- ✅ Heatmaps géographiques
- ✅ Prédictions IA
- ✅ Export PDF/Excel
- ✅ Dark mode

### 🚚 Suivi logistique
- ✅ GPS temps réel
- ✅ Historique trajets
- ✅ Alertes retard
- ✅ Notifications push
- ✅ Optimisation itinéraires
- ✅ Gestion flottes

### 🤖 Intelligence Artificielle
- ✅ Prédiction des retards
- ✅ Optimisation d'itinéraires
- ✅ Analyse des coûts
- ✅ Anomalie detection
- ✅ Recommandations

### 👥 Gestion utilisateurs
- ✅ Authentification JWT
- ✅ Rôles et permissions
- ✅ Gestion chauffeurs
- ✅ Gestion clients
- ✅ Profils détaillés

### 📱 Mobile
- ✅ Suivi GPS
- ✅ Notifications
- ✅ Historique livraisons
- ✅ Chat support

---

## 📈 KPIs suivis

| KPI | Description | Objectif |
|-----|-------------|----------|
| **Taux de ponctualité** | % livraisons à l'heure | > 95% |
| **Coût/km** | Coût moyen par kilomètre | < 5 MAD |
| **Temps moyen livraison** | Durée moyenne | < 4h |
| **Taux de succès** | % livraisons réussies | > 98% |
| **Consommation carburant** | L/100km moyen | < 8L |
| **Productivité chauffeur** | Livraisons/jour moyen | > 15 |
| **Satisfaction client** | Score moyen | > 4.5/5 |

---

## 📚 Documentation

- [Architecture détaillée](./docs/ARCHITECTURE.md)
- [API REST](./docs/API.md)
- [Modèles IA](./docs/AI_MODELS.md)
- [Base de données](./docs/DATABASE.md)
- [Déploiement](./docs/DEPLOYMENT.md)
- [Guide de contribution](./docs/CONTRIBUTING.md)

---

## 🏙️ Context Marocain

La plateforme est optimisée pour le contexte marocain :

**Villes couvertes:**
- Casablanca 🏢
- Rabat 🏛️
- Tanger 🌊
- Marrakech 🕌
- Fès 🏺
- Agadir 🏖️

**Routes principales:**
- A1 Casablanca-Rabat
- A3 Rabat-Fès
- A6 Casablanca-Marrakech
- A7 Agadir-Taroudant

**Données réalistes:**
- Horaires de prière
- Aléas météorologiques
- Configurations géographiques
- Coûts logistiques locaux

---

## 👥 Contribution

Nous accueillons les contributions ! Voir [CONTRIBUTING.md](./docs/CONTRIBUTING.md)

```bash
# Fork le repo
# Créer une branche feature
git checkout -b feature/amazing-feature

# Commit les changements
git commit -m 'Add amazing feature'

# Push vers la branche
git push origin feature/amazing-feature

# Ouvrir une Pull Request
```

---

## 📝 License

Ce projet est sous license MIT. Voir [LICENSE](./LICENSE)

---

## 📧 Support

- 📧 Email: support@logistics-bi-optimizer.com
- 💬 Discord: [Rejoindre le serveur](https://discord.gg/logistics)
- 🐛 Issues: [GitHub Issues](https://github.com/halimakhabbouch20/logistics-bi-optimizer/issues)

---

## 🙏 Remerciements

- Community open-source
- Contributors
- Partenaires logistiques marocains

---

**Made with ❤️ for Moroccan Logistics** 🇲🇦
