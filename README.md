# 🌍 EcoGuard AI
### *Predicting Pollution Before It Happens*

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104-green.svg)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-18.2-blue.svg)](https://reactjs.org/)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.13-orange.svg)](https://www.tensorflow.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Technothon 2024](https://img.shields.io/badge/Technothon-AI%20Battle-red.svg)](https://technothon.ai)

## 🏆 Team AI Winner - Technothon: AI Battle Submission

**EcoGuard AI** is a real-time environmental pollution monitoring and prediction platform that forecasts air quality 24-48 hours in advance using artificial intelligence. Our system helps cities, organizations, and individuals take preventive action against pollution before it reaches dangerous levels.

---

## ✨ Features

### 🎯 **Real-time Pollution Monitoring**
- Live air quality data from 100+ cities worldwide
- Interactive heatmaps showing pollution hotspots
- Multi-parameter tracking (PM2.5, PM10, O3, NO2, SO2, CO)

### 🤖 **AI-Powered Predictions**
- **24-Hour Forecasts**: Accurate pollution predictions using LSTM neural networks
- **Source Attribution**: Identifies pollution sources (vehicles, industry, dust)
- **Anomaly Detection**: Flags sudden pollution spikes using Isolation Forest
- **Spatial Interpolation**: Predicts pollution between monitoring stations

### 🔔 **Smart Alert System**
- Personalized notifications for high pollution levels
- Health recommendations based on pollution severity
- Email, SMS, and push notification support
- Customizable alert thresholds

### 📊 **Comprehensive Dashboard**
- Interactive data visualizations
- Historical trend analysis
- Comparative city analysis
- Mobile-responsive design
- Exportable reports

### 🔧 **Developer-Friendly**
- RESTful API with comprehensive documentation
- Real-time WebSocket updates
- Open data access for research
- Docker containerization for easy deployment

---

## 🏗️ System Architecture

```mermaid
graph TB
    %% EXTERNAL DATA SOURCES
    OWM["OpenWeatherMap API"]
    GOV["Government AQI APIs"]
    SAT["Satellite Data"]
    TRAF["Traffic APIs"]
    
    %% DATA INGESTION LAYER
    subgraph "Data Ingestion Layer"
        INGEST["Data Ingestion Service"]
        KAFKA["Apache Kafka Queue"]
        VALID["Data Validator"]
    end
    
    %% STREAM PROCESSING LAYER
    subgraph "Stream Processing Layer"
        FLINK["Apache Flink Processor"]
        JOIN["Data Joiner"]
        AGG["Window Aggregator"]
    end
    
    %% AI/ML INFERENCE LAYER
    subgraph "AI/ML Layer"
        LSTM["LSTM Time-Series Model"]
        XGB["XGBoost Feature Importance"]
        ANOM["Anomaly Detection"]
        ENSEMBLE["Model Ensemble"]
    end
    
    %% DATA STORAGE LAYER
    subgraph "Data Storage"
        TSDB[("TimescaleDB")]
        REDIS[("Redis Cache")]
        PG[("PostgreSQL")]
        S3[("S3 Bucket")]
    end
    
    %% BACKEND SERVICES
    subgraph "Backend Services"
        API["FastAPI Server"]
        AUTH["Authentication Service"]
        ALERT["Alert Engine"]
        WORKER["Celery Worker"]
    end
    
    %% FRONTEND LAYER
    subgraph "Frontend"
        REACT["React Dashboard"]
        MAP["Mapbox GL Maps"]
        CHARTS["Charts & Graphs"]
        MOBILE["PWA Mobile App"]
    end
    
    %% MONITORING LAYER
    subgraph "Monitoring"
        PROM["Prometheus"]
        GRAF["Grafana"]
        SENTRY["Sentry"]
    end
    
    %% DATA FLOW CONNECTIONS
    OWM --> INGEST
    GOV --> INGEST
    SAT --> INGEST
    TRAF --> INGEST
    
    INGEST --> KAFKA
    KAFKA --> VALID
    VALID --> FLINK
    FLINK --> JOIN
    JOIN --> AGG
    AGG --> LSTM
    AGG --> XGB
    AGG --> ANOM
    
    LSTM --> ENSEMBLE
    XGB --> ENSEMBLE
    ANOM --> ENSEMBLE
    
    ENSEMBLE --> TSDB
    ENSEMBLE --> REDIS
    TSDB --> API
    REDIS --> API
    PG --> API
    S3 --> LSTM
    
    API --> REACT
    API --> MAP
    API --> CHARTS
    API --> MOBILE
    
    API --> ALERT
    ALERT --> PROM
    WORKER --> PROM
    PROM --> GRAF
    API --> SENTRY
```





## 📊 Real-time Data Flow

```mermaid
sequenceDiagram
    participant User as User/Browser
    participant Frontend as React Dashboard
    participant API as FastAPI Server
    participant Cache as Redis Cache
    participant DB as TimescaleDB
    participant Kafka as Kafka Queue
    participant ML as ML Service
    participant Alert as Alert Service

    Note over User,Alert: Step 1: User Loads Dashboard

    User->>Frontend: Load Dashboard Page
    Frontend->>API: GET /api/v1/pollution/current
    API->>Cache: Check cache for latest data

    alt Cache Hit
        Cache-->>API: Return cached data
    else Cache Miss
        API->>DB: Query latest pollution data
        DB-->>API: Return database results
        API->>Cache: Store with 5-min TTL
    end

    API-->>Frontend: Return pollution data
    Frontend-->>User: Display dashboard

    Note over User,Alert: Step 2: Real-time Updates (WebSocket)

    Frontend->>API: Establish WebSocket connection
    API->>Kafka: Subscribe to pollution-updates topic

    loop Every minute
        Kafka-->>API: Stream real-time pollution data
        API-->>Frontend: Push updates via WebSocket
        Frontend-->>User: Update UI in real-time
    end

    Note over User,Alert: Step 3: Scheduled Data Processing

    loop Every 5 minutes
        API->>Kafka: Publish new pollution readings
        Kafka->>ML: Trigger ML prediction
        ML->>ML: Run LSTM model inference
        ML->>DB: Store 24-hour predictions
        ML->>Kafka: Publish prediction results
        Kafka->>Alert: Check alert thresholds

        alt Threshold Exceeded
            Alert->>Alert: Generate alert notification
            Alert->>User: Send email/SMS notification
        end
    end

    Note over User,Alert: Step 4: User Requests Historical Data

    User->>Frontend: Click "View History"
    Frontend->>API: GET /api/v1/pollution/historical
    API->>DB: Query time-series data
    DB-->>API: Return historical records
    API-->>Frontend: Return chart-ready data
    Frontend-->>User: Display historical charts
```






## 🗄️ Database Schema
```mermaid
erDiagram
    USERS {
        uuid id PK
        string email UK
        string username
        string password_hash
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }
    
    PREFERENCES {
        uuid id PK
        uuid user_id FK
        jsonb alert_thresholds
        string[] monitored_cities
        boolean email_alerts
        boolean sms_alerts
        boolean push_notifications
    }
    
    CITIES {
        uuid id PK
        string name
        string country_code
        float latitude
        float longitude
        integer population
        string timezone
    }
    
    POLLUTION_DATA {
        uuid id PK
        uuid city_id FK
        timestamp measured_at
        float pm2_5
        float pm10
        float ozone
        float nitrogen_dioxide
        float sulfur_dioxide
        float carbon_monoxide
        float air_quality_index
        float temperature
        float humidity
        float wind_speed
    }
    
    PREDICTIONS {
        uuid id PK
        uuid city_id FK
        timestamp predicted_for
        float pm2_5_predicted
        float confidence_score
        string model_version
        timestamp created_at
    }
    
    ALERTS {
        uuid id PK
        uuid user_id FK
        uuid city_id FK
        string severity_level
        string alert_message
        boolean is_read
        timestamp triggered_at
        timestamp sent_at
    }
    
    EXTERNAL_SOURCES {
        uuid id PK
        string source_name
        string api_endpoint
        boolean is_active
        timestamp last_fetched
    }
    
    USERS ||--o{ ALERTS : "receives"
    USERS ||--o{ PREFERENCES : "configures"
    CITIES ||--o{ POLLUTION_DATA : "contains"
    POLLUTION_DATA ||--o{ PREDICTIONS : "generates"
    EXTERNAL_SOURCES ||--o{ POLLUTION_DATA : "provides"
```
