# E.C.H.O. - Earthquake & Conflict Hazard Observer

Distributed platform for real-time seismic monitoring and autonomous classification of natural events and human threats.

## Overview
E.C.H.O. is a fault-tolerant monitoring system designed to acquire high-frequency data from a distributed network of seismic sensors. It operates 24/7 to ensure real-time processing and data consistency, even during partial infrastructure failures.

## Event Classification
The system analyzes 5-second windows of seismic data (100 samples at 20 Hz) using Fast Fourier Transform (FFT) to classify events in near real-time:
* **Natural Earthquakes** (0.5 - 3.0 Hz)
* **Conventional Explosions** (3.0 - 8.0 Hz)
* **Nuclear Tests** (>= 8.0 Hz)

Each event is assigned a Severity Score (0-100) based on the Signal-to-Noise Ratio to help analysts prioritize responses.

## Architecture & Tech Stack
The project is built with a microservices architecture, entirely containerized using Docker and docker-compose.

* **Gateway:** Nginx acts as a reverse proxy, handling rate limiting and passive health checks.
* **Ingestion Broker:** Manages persistent WebSocket connections with sensors and uses a Fan-Out pattern to distribute data to processing replicas.
* **Idempotency:** To prevent duplicate events from the Fan-Out pattern, PostgreSQL handles data uniqueness using a deterministic UUIDv5 (Sensor_ID + Timestamp + Event_Type) and the `ON CONFLICT DO NOTHING` constraint.
* **Resilience:** Implemented a Circuit Breaker pattern with auto-healing to protect processing replicas from database outages.
* **Chaos Engineering:** A built-in SSE simulator to test fault injection (node shutdowns) and failover in real-time.

## Signal Processing (DSP)
To ensure accurate classification and prevent anomalies, the raw signal is preprocessed:
* **Hanning Window:** Applied before the FFT to prevent spectral leakage and edge discontinuities.
* **High-Pass Filter (0.2 Hz):** Removes 1/f pink noise and baseline thermal drift.

## Command Dashboard
The frontend is a React Single Page Application (SPA). Analysts can view incoming events in real-time via WebSockets (no polling), track incidents on interactive maps, and export strategic reports.

## The Team
Final project for the Laboratory of Advanced Programming (2025/2026).
Developed by: 
* Fabiano Cacioli
* Jacopo Rossi
* Fabrizio Pietrobono
* Emanuele Smisi
* Luca Buonomini
