# LogisticsBI Optimizer 🚚📊

Système décisionnel pour optimisation logistique marocaine

## 📋 Vue d'ensemble

LogisticsBI Optimizer est une plateforme BI complète conçue pour analyser et optimiser les opérations logistiques au Maroc. Elle intègre:
- **Suivi en temps réel** des livraisons
- **Dashboard analytique** interactif
- **Optimisation d'itinéraires** par IA
- **Prédiction des retards**
- **Traçabilité blockchain** (bonus)

## 🎯 Objectifs

✅ Améliorer la visibilité des opérations logistiques
✅ Optimiser les coûts de livraison
✅ Exploiter efficacement les données
✅ Prédire et prévenir les retards
✅ Fournir un framework BI open-source pour l'industrie

## 🏗️ Architecture Technique

```
┌───────────────────────────────��──────────────────────────────────┐
│                     FRONTEND LAYER                               │
├──────────────────────────────┬──────────────────────────────────┤
│   Mobile App                 │      Web Dashboard               │
│   (Flutter)                  │      (Vue.js + D3.js)            │
└──────────────────────────────┴──────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│              API GATEWAY & SERVICE LAYER                         │
│              (Node.js + Express)                                 │
├──────────────────────────────────────────────────────────────────┤
│  • Routes Management    • Analytics Service                      │
│  • Auth Service         • Prediction Engine                      │
│  • Real-time Tracking   • Optimization Service                   │
└──────────────────────────────────────────────────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│           DATA LAYER & BI COMPONENTS                             │
├───────────────────────┬───────────────┬────────────────────┤
│  Data Warehouse       │  ETL Pipeline │  BI Analytics      │
│  (PostgreSQL)         │  (Airflow)    │  (Superset)        │
└───────────────────────┴───────────────┴────────────────────┘
                            ↓
┌──────────────────────────────────────────────────────────────────┐
│         DATA SOURCES & EXTERNAL INTEGRATIONS                     │
├───────────────────────┬───────────────┬────────────────────┤
│  GPS Tracking         │  Order System │  Weather API       │
│  (Real-time)          │  (Logistics)  │  (Prévisions)      │
└───────────────────────┴───────────────┴────────────────────┘
```

## 📂 Structure du Projet

```
logistics-bi-optimizer/
├── 📂 docs/                          # Documentation
│   ├── UML_Architecture.png          # Diagrammes UML (PNG)
│   ├── ARCHITECTURE.md               # Guide d'architecture
│   ├── DATA_SOURCES.md               # Sources de données marocaines
│   ├── API_DOCUMENTATION.md          # Documentation API
│   └── DEPLOYMENT.md                 # Guide de déploiement
│
├── 📂 backend/                       # Serveur Node.js
│   ├── src/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── services/
│   │   ├── middleware/
│   │   └── app.js
│   ├── docker/
│   ├── .env.example
│   ├── package.json
│   └── Dockerfile
│
├── 📂 frontend-web/                  # Application Web (Vue.js)
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── store/
│   │   └── App.vue
│   ├── public/
│   ├── package.json
│   └── vue.config.js
│
├── 📂 frontend-mobile/               # Application Mobile (Flutter)
│   ├── lib/
│   │   ├── screens/
│   │   ├── services/
│   │   ├── models/
│   │   └── main.dart
│   ├── pubspec.yaml
│   └── android/ios/
│
├── 📂 data-warehouse/                # Data Warehouse & ETL
│   ├── scripts/
│   │   ├── create_schema.sql
│   │   ├── etl_pipeline.py
│   │   └── sample_data.sql
│   ├── dags/                         # Airflow DAGs
│   └── docker-compose.yml
│
├── 📂 bi-analytics/                  # Apache Superset Config
│   ├── dashboards/
│   ├── datasets/
│   ├── docker-compose.yml
│   └── config.py
│
├── 📂 ai-engine/                     # Machine Learning & Optimization
│   ├── models/
│   │   ├── route_optimizer.py
│   │   ├── delay_predictor.py
│   │   └── cost_analyzer.py
│   ├── training/
│   └── requirements.txt
│
├── 📂 blockchain/                    # (Bonus) Blockchain Module
│   ├── contracts/
│   ├── truffle-config.js
│   └── README.md
│
├── 📂 deployment/                    # Scripts de déploiement
│   ├── docker-compose.yml
│   ├── kubernetes/
│   ├── ci-cd/
│   └── env-config/
│
├── docker-compose.yml                # Stack complet
├── .github/
│   └── workflows/                    # CI/CD Pipelines
├── .gitignore
└── README.md
```

## 🔧 Technologies Utilisées

| Composant | Technologies |
|-----------|---------------|
| **Frontend Web** | Vue.js 3, Vuex, Axios, D3.js, Leaflet |
| **Frontend Mobile** | Flutter, Dart, Provider |
| **Backend** | Node.js, Express.js, Socket.io (temps réel) |
| **Base de Données** | PostgreSQL, Redis (cache) |
| **BI & Analytics** | Apache Superset, Jupyter |
| **Data Pipeline** | Apache Airflow, Python |
| **ML/AI** | TensorFlow, Scikit-learn, Optuna |
| **Blockchain** | Ethereum, Solidity, Truffle |
| **Déploiement** | Docker, Docker Compose, Kubernetes |
| **CI/CD** | GitHub Actions, Jenkins |

## 📊 Sources de Données Marocaines

### Sources Officielles
1. **OpenStreetMap Maroc** - Réseau routier complet
2. **Haut-Commissariat au Plan (HCP)** - Statistiques économiques
3. **Agence Nationale de la Conservation Foncière** - Données cadastrales
4. **Ministère de l'Équipement et de l'Eau** - Réseaux routiers
5. **Direction de la Météorologie Nationale** - Données climatiques

### Villes Majeures (Focus Logistique)
- 🏙️ **Casablanca** - Centre économique (3.74M hab)
- 🏙️ **Fès** - Hub nord-oriental (1.11M hab)
- 🏙️ **Marrakech** - Hub touristique (928K hab)
- 🏙️ **Tanger** - Porte nord (948K hab)
- 🏙️ **Rabat** - Capitale (580K hab)

### Routes Majeures
- **A1** (Rabat-Fès): 470 km
- **A3** (Fès-Oujda): 465 km
- **A6** (Casablanca-Marrakech): 240 km
- **A7** (Casablanca-Agadir): 540 km

## 🚀 Démarrage Rapide

### Prérequis
- Docker & Docker Compose
- Node.js 18+
- Python 3.9+
- Flutter SDK (pour mobile)

### Installation

```bash
# Cloner le projet
git clone https://github.com/halimakhabbouch20/logistics-bi-optimizer.git
cd logistics-bi-optimizer

# Démarrer tous les services
docker-compose up -d

# Initialiser la base de données
sh ./deployment/init-db.sh

# Accéder aux applications
# Web: http://localhost:8080
# API: http://localhost:3000
# BI: http://localhost:8088
```

## 📈 Indicateurs Clés (KPIs)

✅ **Temps de livraison moyen** : Réduction de 20%
✅ **Coût par km** : Optimisation de 15%
✅ **Taux de prédiction de retards** : >85% accuracy
✅ **Temps de réponse API** : <200ms (p99)
✅ **Disponibilité du service** : 99.5%

## 📚 Documentation Complète

Voir le dossier `docs/` pour :
- Architecture détaillée
- Diagrammes UML
- Guide d'installation
- Documentation API
- Guide de déploiement
- Sources de données avec références

## 🤝 Contribution

Ce projet est open-source et bienvenue aux contributions:

1. Fork le projet
2. Créer une branche (`git checkout -b feature/AmazingFeature`)
3. Commit les changements (`git commit -m 'Add AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

## 📞 Support

Pour toute question ou problème:
- 📧 Email: halimakhabbouch20@github.com
- 📝 Issues: [GitHub Issues](https://github.com/halimakhabbouch20/logistics-bi-optimizer/issues)
- 💬 Discussions: [GitHub Discussions](https://github.com/halimakhabbouch20/logistics-bi-optimizer/discussions)

## 📄 License

Ce projet est sous licence **MIT** - voir le fichier `LICENSE` pour plus de détails.

## 🙏 Remerciements

- OpenStreetMap Maroc
- Communauté open-source mondiale
- Experts logistiques marocains

---

**Dernière mise à jour**: 2026-05-14
**Version**: 1.0.0-alpha
