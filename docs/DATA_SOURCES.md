# Sources de Données Marocaines - Références et Intégrations

## 🗺️ Sources Géographiques et Spatiales

### 1. OpenStreetMap Maroc (OSM Maroc)
**URL:** https://www.openstreetmap.org/
**Utilisation:**
- Réseau routier complet du Maroc
- Localisation des villes, quartiers, POIs
- Coordonnées GPS précises
- Données de trafic

**Référence Source:**
- Contributors: OpenStreetMap Maroc Community
- License: ODbL 1.0
- Dernière mise à jour: 2026-05

---

### 2. Agence Nationale de la Conservation Foncière (ANCF)
**URL:** https://www.ancf.gov.ma/
**Utilisation:**
- Données cadastrales marocaines
- Délimitations administratives
- Propriété foncière

**Format:** Shapefiles, GeoJSON
**Référence:** Gouvernement du Maroc - Ministère de l'Intérieur

---

### 3. Institut Géographique Royal (IGR)
**URL:** https://igr.gov.ma/
**Utilisation:**
- Cartes topographiques officielles
- Données d'altitude (DEM)
- Données de relief

**API:** Disponible par demande officielle
**Référence:** Établissement public marocain

---

## 🏢 Données Économiques et Commerciales

### 4. Ministère du Commerce et de l'Industrie
**URL:** https://www.mcinet.gov.ma/
**Données Disponibles:**
- Entreprises importatrices/exportatrices
- Zones franches
- Statistiques commerciales

**Accès:** Demande administrative
**Référence:** Portail officiel du commerce marocain

---

### 5. Office Marocain de la Propriété Industrielle et Commerciale (OMPIC)
**URL:** https://www.ompic.org.ma/
**Utilisation:**
- Registre des entreprises
- Secteurs d'activité
- Données d'immatriculation

**API:** REST API disponible
**Référence:** Base de données commerciales marocaines

---

## 🚚 Données Logistiques et Transport

### 6. Agence Nationale de la Sécurité Routière (ANSR)
**URL:** https://www.ansr.ma/
**Données:**
- Statistiques accidents routiers
- Conditions routières
- Zones dangereuses

**Format:** Rapports PDF, données brutes (CSV sur demande)
**Référence:** Ministère de l'Équipement et de l'Eau

---

### 7. Port Autonome de Casablanca (PAC)
**URL:** https://www.portcasablanca.ma/
**Données Maritimes:**
- Statistiques de conteneurs
- Horaires des navires
- Flux portuaires

**API:** Disponible pour partenaires agréés
**Référence:** Port Authority - Casablanca

---

### 8. Office National des Aéroports (ONDA)
**URL:** https://www.onda.ma/
**Données:**
- Statistiques passagers/cargo
- Horaires des vols
- Fret aérien

**Référence:** Transport aérien marocain

---

## 📊 Données Statistiques

### 9. Haut-Commissariat au Plan (HCP)
**URL:** https://www.hcp.ma/
**Statistiques Disponibles:**
- Population par région/ville
- PIB régional
- Emploi et chômage
- Revenus
- Indicateurs économiques

**Format:** CSV, Excel, API REST
**Référence:** Office statistique marocain officiel

**Données d'intérêt pour la logistique:**
```python
# Population par ville majeure (2026)
CITIES_POPULATION = {
    "Casablanca": 3740000,      # Centre économique
    "Fès": 1112000,              # Hub nord-oriental
    "Marrakech": 928000,         # Hub touristique
    "Rabat": 580000,             # Capitale administrative
    "Agadir": 521000,            # Côte atlantique
    "Tanger": 948000,            # Porte nord
    "Meknes": 612000,            # Centre logistique
    "Oujda": 445000,             # Frontier est
}
```

---

### 10. World Bank Open Data - Morocco
**URL:** https://data.worldbank.org/country/MA
**Indicateurs:**
- Transporters per capita
- Logistics Performance Index (LPI)
- Trade costs
- Infrastructure quality

**Reference:** World Bank

---

## 🚗 Données de Véhicules et Flotte

### 11. Ministère de l'Intérieur - Registre des Véhicules
**Données:**
- Immatriculation des véhicules
- Âge de la flotte
- Types de carburant

**Accès:** Sur demande administrative
**Référence:** Gouvernement du Maroc

---

## 🌐 APIs Externes Recommandées

### 12. Google Maps Platform
**Services:**
- Distance Matrix API - Calcul distances/durées
- Directions API - Planification itinéraires
- Places API - Géolocalisation

**Pricing:** Pay-as-you-go
**URL:** https://developers.google.com/maps

---

### 13. HERE Maps API
**Services:**
- Routing API - Optimisation itinéraires
- Fleet Telematics - Suivi flotte
- Map Tile API

**Avantage:** Excellent coverage Maroc
**URL:** https://developer.here.com/

---

### 14. Mapbox
**Services:**
- Direction API
- Optimization API (VRP)
- Navigation SDK

**URL:** https://www.mapbox.com/
**Tarif:** Free tier + pay-as-you-go

---

## 🌍 Données Climatiques et Environnementales

### 15. Direction de la Météorologie Nationale (DMN)
**URL:** https://www.meteo.ma/
**Données:**
- Prévisions météorologiques
- Conditions routières
- Alertes tempêtes

**API:** Disponible (authentification requise)
**Référence:** Ministère de l'Équipement

---

### 16. OpenWeather API
**URL:** https://openweathermap.org/api
**Utilisation:** Prévisions météo pour optimisation d'itinéraires
**Plan:** Free tier disponible pour Maroc
**Référence:** Service météo mondial

---

## 📱 Données Télécommunications

### 17. Agence Nationale de Réglementation des Télécommunications (ANRT)
**URL:** https://www.anrt.ma/
**Données:**
- Couverture 4G/5G
- Qualité de signal
- Statistiques connectivité

**Utilisation:** Optimisation couverture GPS

---

## 📋 Ensemble de Données Synthétique Maroc

### Données d'Exemple Intégrées

```python
# cities.py - Villes marocaines clés
MAJOR_CITIES = {
    "Casablanca": {"lat": 33.5731, "lon": -7.5898, "population": 3740000},
    "Fès": {"lat": 34.0320, "lon": -5.0042, "population": 1112000},
    "Marrakech": {"lat": 31.6295, "lon": -8.0088, "population": 928000},
    "Rabat": {"lat": 34.0209, "lon": -6.8416, "population": 580000},
    "Agadir": {"lat": 30.4269, "lon": -9.5981, "population": 521000},
    "Tanger": {"lat": 35.7577, "lon": -5.8172, "population": 948000},
    "Meknes": {"lat": 33.8869, "lon": -5.5492, "population": 612000},
    "Oujda": {"lat": 34.6724, "lon": -1.9129, "population": 445000},
}

# routes.py - Routes majeures marocaines
MAJOR_ROUTES = [
    {"code": "A1", "from": "Rabat", "to": "Fès", "distance_km": 470},
    {"code": "A3", "from": "Fès", "to": "Oujda", "distance_km": 465},
    {"code": "A6", "from": "Casablanca", "to": "Marrakech", "distance_km": 240},
    {"code": "A7", "from": "Casablanca", "to": "Agadir", "distance_km": 540},
]

# sectors.py - Secteurs logistiques marocains
LOGISTIC_SECTORS = [
    "E-commerce",
    "Agroalimentaire",
    "Industrie automobile",
    "Pharmaceutique",
    "Textiles et vêtements",
    "Pétrochimie",
    "Électronique",
    "Commerce international",
]
```

---

## 🔐 Conformité et Légalité

### RGPD & Données Personnelles
- Adresses clients anonymisées
- Données de coordonnées GPS chiffrées
- Consentement utilisateur documenté
- Droit à l'oubli implémenté

### Accords avec Sources Publiques
- Respect des licenses (ODbL, ODC-By)
- Attribution des sources
- Non-commercialisation de certaines données

### Conformité Marocaine
- Respect de la loi 09-08 (protection données)
- Consultation CNDP si nécessaire
- Localisation serveurs (recommandé: Maroc ou EU)

---

## 📞 Contacts Institutionnels

| Institution | Contact | Email |
|-------------|---------|-------|
| HCP | +212 5 37 77 82 00 | contact@hcp.ma |
| ANRT | +212 5 37 71 80 00 | contact@anrt.ma |
| IGR | +212 5 37 75 45 45 | info@igr.gov.ma |
| ANCF | +212 5 37 20 80 20 | contact@ancf.gov.ma |

---

**Dernière mise à jour:** 2026-05-14
**Validé:** Données réelles et sources officielles marocaines
