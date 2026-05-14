# API Documentation - LogisticsBI Optimizer

## 📡 Base URL

```
https://api.logistics-bi.example.com/v1
```

## 🔐 Authentication

### JWT Token

Tous les endpoints (sauf `/auth/login`) nécessitent un JWT token dans le header:

```bash
Authorization: Bearer <jwt_token>
```

### Login

```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}

# Response 200
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "user_123",
    "email": "user@example.com",
    "role": "manager"
  },
  "expiresIn": "24h"
}
```

---

## 📦 Endpoints Livraisons

### 1. Créer une livraison

```http
POST /deliveries
Content-Type: application/json
Authorization: Bearer <token>

{
  "orderId": "ORDER_123",
  "customerId": "CUST_456",
  "origin": {
    "address": "Casablanca Port, Morocco",
    "latitude": 33.5731,
    "longitude": -7.5898
  },
  "destination": {
    "address": "Fes City Center, Morocco",
    "latitude": 34.0320,
    "longitude": -5.0042
  },
  "vehicleId": "VEH_789",
  "driverId": "DRV_001",
  "estimatedDuration": 480,
  "weight": 150,
  "volume": 2.5
}

# Response 201
{
  "id": "DEL_12345",
  "status": "pending",
  "createdAt": "2026-05-14T10:30:00Z",
  "updatedAt": "2026-05-14T10:30:00Z"
}
```

### 2. Récupérer toutes les livraisons

```http
GET /deliveries?status=in_transit&limit=50&offset=0
Authorization: Bearer <token>

# Response 200
{
  "data": [
    {
      "id": "DEL_12345",
      "orderId": "ORDER_123",
      "status": "in_transit",
      "currentLocation": {
        "latitude": 33.9716,
        "longitude": -6.8498,
        "timestamp": "2026-05-14T11:45:00Z"
      },
      "progress": 0.45,
      "estimatedArrival": "2026-05-14T15:30:00Z"
    }
  ],
  "pagination": {
    "total": 150,
    "limit": 50,
    "offset": 0
  }
}
```

### 3. Tracking GPS en temps réel

```http
GET /deliveries/:deliveryId/tracking
Authorization: Bearer <token>

# Response 200 (WebSocket)
{
  "type": "location_update",
  "deliveryId": "DEL_12345",
  "location": {
    "latitude": 33.9716,
    "longitude": -6.8498,
    "speed": 65,
    "heading": 315
  },
  "timestamp": "2026-05-14T11:45:00Z"
}
```

### 4. Mettre à jour le statut

```http
PATCH /deliveries/:deliveryId
Content-Type: application/json
Authorization: Bearer <token>

{
  "status": "delivered",
  "actualDuration": 475,
  "feedback": "Successfully delivered"
}

# Response 200
{
  "id": "DEL_12345",
  "status": "delivered",
  "updatedAt": "2026-05-14T15:35:00Z"
}
```

---

## 🗺️ Endpoints Routes

### 1. Optimiser une route (VRP)

```http
POST /routes/optimize
Content-Type: application/json
Authorization: Bearer <token>

{
  "stops": [
    {
      "id": "STOP_1",
      "location": {"latitude": 33.5731, "longitude": -7.5898},
      "type": "depot"
    },
    {
      "id": "STOP_2",
      "location": {"latitude": 34.0320, "longitude": -5.0042},
      "type": "delivery",
      "timeWindow": {"start": "09:00", "end": "17:00"}
    },
    {
      "id": "STOP_3",
      "location": {"latitude": 31.6295, "longitude": -8.0088},
      "type": "delivery"
    }
  ],
  "vehicles": [
    {
      "id": "VEH_001",
      "capacity": 500,
      "startLocation": {"latitude": 33.5731, "longitude": -7.5898}
    }
  ],
  "constraints": {
    "maxDuration": 480,
    "maxDistance": 1000
  }
}

# Response 200
{
  "routeId": "ROUTE_123",
  "optimizedStops": [
    {"stopId": "STOP_1", "sequence": 1, "arrivalTime": "09:00"},
    {"stopId": "STOP_2", "sequence": 2, "arrivalTime": "10:30"},
    {"stopId": "STOP_3", "sequence": 3, "arrivalTime": "13:15"}
  ],
  "metrics": {
    "totalDistance": 850,
    "totalDuration": 420,
    "totalCost": 1250
  }
}
```

### 2. Récupérer une route

```http
GET /routes/:routeId
Authorization: Bearer <token>

# Response 200
{
  "id": "ROUTE_123",
  "vehicleId": "VEH_001",
  "status": "in_progress",
  "stops": [...],
  "metrics": {
    "plannedDistance": 850,
    "actualDistance": 865,
    "plannedDuration": 420,
    "actualDuration": 438,
    "costBreakdown": {
      "fuel": 120,
      "tolls": 80,
      "labor": 50
    }
  }
}
```

---

## 📊 Endpoints Analytics

### 1. KPIs Dashboard

```http
GET /analytics/kpis?startDate=2026-05-01&endDate=2026-05-14
Authorization: Bearer <token>

# Response 200
{
  "metrics": {
    "totalDeliveries": 1250,
    "successfulDeliveries": 1198,
    "failedDeliveries": 52,
    "averageDeliveryTime": 425,
    "averageCostPerKm": 1.85,
    "costPerDelivery": 45.30,
    "driverUtilization": 0.78,
    "onTimePercentage": 0.958
  },
  "trends": {
    "deliveriesTrend": [230, 245, 210, 198, 205, 210, 220],
    "costTrend": [8500, 9120, 8450, 7980, 8200, 8350, 8600]
  }
}
```

### 2. Performance par chauffeur

```http
GET /analytics/drivers?limit=10&sortBy=rating
Authorization: Bearer <token>

# Response 200
{
  "drivers": [
    {
      "id": "DRV_001",
      "name": "Ahmed Hassan",
      "totalDeliveries": 150,
      "successRate": 0.98,
      "averageRating": 4.8,
      "averageTime": 410,
      "totalKmDriven": 8500
    }
  ]
}
```

### 3. Délais et retards

```http
GET /analytics/delays?city=Casablanca&period=week
Authorization: Bearer <token>

# Response 200
{
  "totalDeliveries": 350,
  "delayedDeliveries": 12,
  "delayPercentage": 3.43,
  "averageDelay": 25,
  "delays": [
    {
      "deliveryId": "DEL_123",
      "delayMinutes": 35,
      "reason": "Traffic",
      "location": "Casablanca Center"
    }
  ]
}
```

---

## 🤖 Endpoints AI/Prediction

### 1. Prédire les retards

```http
POST /predictions/delay
Content-Type: application/json
Authorization: Bearer <token>

{
  "deliveryId": "DEL_12345",
  "distance": 850,
  "timeOfDay": "14:30",
  "dayOfWeek": "Wednesday",
  "weatherCondition": "rainy",
  "trafficLevel": "medium",
  "driverId": "DRV_001"
}

# Response 200
{
  "delayProbability": 0.25,
  "estimatedDelay": 15,
  "confidenceScore": 0.87,
  "factors": {
    "traffic": "medium impact",
    "weather": "high impact",
    "driver_experience": "low impact"
  }
}
```

### 2. Prévisions de demande

```http
GET /predictions/demand?region=Casablanca&days=7
Authorization: Bearer <token>

# Response 200
{
  "forecast": [
    {"date": "2026-05-15", "expectedOrders": 250, "confidence": 0.91},
    {"date": "2026-05-16", "expectedOrders": 280, "confidence": 0.88}
  ]
}
```

---

## ❌ Codes d'erreur

| Code | Description |
|------|-------------|
| 400  | Bad Request - Paramètres invalides |
| 401  | Unauthorized - Token invalide/expiré |
| 403  | Forbidden - Permissions insuffisantes |
| 404  | Not Found - Ressource inexistante |
| 409  | Conflict - Violation de contrainte |
| 429  | Too Many Requests - Rate limit dépassé |
| 500  | Internal Server Error |
| 503  | Service Unavailable |

---

## 📚 Postman Collection

Importer la collection Postman:
```
https://github.com/halimakhabbouch20/logistics-bi-optimizer/raw/main/docs/postman-collection.json
```

---

**Dernière mise à jour:** 2026-05-14
**Version API:** 1.0.0
