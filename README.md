# PW3-Data-Hose

A comprehensive telemetry application for the Tesla Powerwall 3, designed to interface with the Tesla Fleet API. This application securely authenticates, ingests, stores, and visualizes high-frequency energy data, providing users with granular insights into their solar generation, home usage, and battery performance.

## 📚 Documentation
- [Requirements & Design Specification](./REQUIREMENTS.md)
- [Technical Standards](./TECHNICAL_STANDARDS.md)

## ✨ Features

- **Secure Authentication**: Implements the Tesla Third-Party Token flow (OAuth 2.0) with automated token rotation and storage.
- **Data Ingestion**: Background workers ("Poller") that fetch live status and historical data from the Powerwall 3.
- **Time-Series Storage**: Optimized storage for energy metrics (Solar, Grid, Battery, Load) using PostgreSQL.
- **Interactive Visualization**: Angular-based dashboard with real-time power flow animations and historical data charts.
- **PKI & Compliance**: Hosts necessary public keys for Tesla Fleet API domain verification.

## 🏗 System Architecture

The project follows a decoupled architecture, separating the backend ingestion logic from the frontend presentation layer.

- **Backend**: Spring Boot v3.5.9 (Java 21)
- **Frontend**: Angular 20 with Angular Material
- **Database**: PostgreSQL (pgvector support available)
- **Hosting**: Heroku (supports Docker/Local dev)

## 🛠 Prerequisites

Before running the application, ensure you have the following installed:

- **Java JDK 21**: Required for the backend.
- **Node.js & npm**: Required for the Angular frontend.
- **Docker**: Used for running the local PostgreSQL database.
- **Tesla Developer Account**: You need a Client ID and Secret from the [Tesla Developer Portal](https://developer.tesla.com/).

## 🚀 Getting Started

### 1. Database Setup
Start the local PostgreSQL instance using Docker:
```bash
docker run --name pw3-postgres -e POSTGRES_PASSWORD=password -p 5432:5432 -d postgres
```
*Note: The application expects `jdbc:postgresql://localhost:5432/postgres` locally.*

### 2. Backend Setup
Navigate to the backend directory (once created) or root:
```bash
# Example command (adjust based on actual project structure when implemented)
mvn clean install
mvn spring-boot:run
```
The backend will run on `http://localhost:8080`.

### 3. Frontend Setup
Navigate to the frontend directory:
```bash
# Example command
npm install
npm start
```
The frontend will run on `http://localhost:4200` and proxy API requests to the backend.

## ⚙️ Configuration

The application uses **Spring Profiles** for environment management:
- **`dev`**: Local development (local Docker DB)
- **`staging` / `prod`**: Heroku environments (Heroku Postgres)

Key environment variables to configure (do not commit these):
- `TESLA_CLIENT_ID`
- `TESLA_CLIENT_SECRET`
- `DB_USERNAME`
- `DB_PASSWORD`

## 🤝 Contributing

Please review the [Technical Standards](./TECHNICAL_STANDARDS.md) before contributing.
- Follow Google Java Style Guide.
- Contextualize commits: `feat:`, `fix:`, `docs:`, etc.
