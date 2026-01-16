# Design Specification: Tesla Powerwall 3 Telemetry Application

## 1. Executive Summary
This document outlines the architectural design and functional requirements for a web application capable of interfacing with the Tesla Fleet API. The primary goal is to securely authenticate, ingest, store, and visualize time-series data from a Powerwall 3 battery system. This design remains technology-agnostic to allow for flexible implementation standards.

---

## 2. System Architecture

The system utilizes a decoupled architecture, separating data ingestion (background processes) from data presentation (user-facing interface).

### 2.1 Core Components

#### **A. Public Key Infrastructure (PKI) Host**
* **Purpose:** Verification of domain ownership required by Tesla Fleet API.
* **Requirement:** Must serve a static public key file over HTTPS at `/.well-known/appspecific/com.tesla.3p.public-key.pem`.

#### **B. Authentication Service**
* **Purpose:** Manages the OAuth 2.0 lifecycle.
* **Responsibilities:**
    * Handling the "Third-Party Token" exchange flow.
    * Secure storage of `access_token` and `refresh_token`.
    * **Automated Rotation:** A scheduled task to refresh tokens before expiration (tokens are short-lived).

#### **C. Data Ingestion Worker (The "Poller")**
* **Purpose:** Background service for telemetry extraction.
* **Logic:**
    * Retrieves valid credentials from the Auth Service.
    * Polls Tesla endpoints based on a defined schedule (e.g., 5-minute intervals).
    * Parses payloads (Solar, Grid, Battery, Load) and normalizes data.

#### **D. Time-Series Data Store**
* **Purpose:** Optimized storage for high-frequency energy metrics.
* **Conceptual Schema:**
    * `timestamp` (UTC)
    * `site_id` (UUID)
    * `metric_name` (Enum: solar, grid, battery, load)
    * `value` (Float/Integer)

#### **E. API Layer**
* **Purpose:** Interface between the Data Store and the Frontend.
* **Security:** Enforces session-based authentication to prevent unauthorized access to energy data.

#### **F. Frontend Application**
* **Purpose:** Data visualization dashboard.
* **Key Views:**
    * **Live Flow:** Real-time power distribution.
    * **History:** Interactive time-series charts.

---

## 3. API Interfaces (Tesla Fleet API)

The application will interface with the Tesla Energy endpoints using the **Third-Party Token** flow.

### 3.1 Authentication & Authorization
* **Protocol:** OAuth 2.0 (Authorization Code Grant).
* **Required Scopes:**
    * `openid`: Identity verification.
    * `offline_access`: Required for refreshing tokens without user intervention.
    * `energy_device_data`: Read-only access to Powerwall telemetry.

### 3.2 Critical Endpoints
1.  **Discovery:** `GET /api/1/products`
    * *Usage:* To retrieve the unique `energy_site_id` for the Powerwall 3.
2.  **Telemetry:** `GET /api/1/energy_sites/{site_id}/live_status`
    * *Usage:* High-frequency polling for current power flow.
3.  **Reconciliation:** `GET /api/1/energy_sites/{site_id}/calendar_history`
    * *Usage:* Batch retrieval of historical data to backfill gaps if the Poller is offline.

---

## 4. Epics and User Stories

### Epic 1: System Foundation & Compliance
**Goal:** Establish the secure environment required to communicate with Tesla.

* **Story 1.1: Public Key Hosting**
    * *As a* System Admin,
    * *I want* to host a verified public key at a `.well-known` URL,
    * *So that* Tesla can verify my application identity during the handshake.

* **Story 1.2: Application Registration**
    * *As a* Developer,
    * *I want* to configure the application with the Client ID and Client Secret from the Tesla Developer Portal,
    * *So that* the application is authorized to initiate OAuth flows.

### Epic 2: Authentication (The "Handshake")
**Goal:** Securely acquire and maintain access to the Powerwall data.

* **Story 2.1: User Authorization Flow**
    * *As a* User,
    * *I want* to initiate a "Connect Tesla Account" workflow,
    * *So that* I can grant permissions via Tesla's secure login page without sharing my password.

* **Story 2.2: Token Lifecycle Management**
    * *As a* System,
    * *I want* to securely store and automatically rotate the `refresh_token`,
    * *So that* data collection persists indefinitely without requiring me to re-login every day.

### Epic 3: Data Ingestion
**Goal:** Reliably extract telemetry from the Powerwall 3.

* **Story 3.1: Site Discovery**
    * *As a* System,
    * *I want* to automatically detect the Powerwall's `energy_site_id` upon first connection,
    * *So that* subsequent API calls are routed to the correct hardware.

* **Story 3.2: Live Status Polling**
    * *As a* System,
    * *I want* to fetch the live status endpoint at a configurable interval,
    * *So that* I have granular insight into energy production and consumption.

* **Story 3.3: Data Normalization**
    * *As a* Data Engineer,
    * *I want* to flatten nested JSON responses into a standardized schema,
    * *So that* the data is optimized for time-series queries.

### Epic 4: Visualization & Analysis
**Goal:** specific display of stored data to the user.

* **Story 4.1: Historical Energy API**
    * *As a* Frontend Developer,
    * *I want* an internal API endpoint that accepts date range parameters,
    * *So that* I can request only the data needed for the current view.

* **Story 4.2: Energy Flow Chart**
    * *As a* User,
    * *I want* a multi-line chart showing Solar, Grid, and Home usage over time,
    * *So that* I can visually correlate my usage peaks with solar generation.

* **Story 4.3: Battery SoC Overlay**
    * *As a* User,
    * *I want* to overlay my Battery State of Charge (%) onto the usage graph,
    * *So that* I can see how charging/discharging correlates with solar availability.