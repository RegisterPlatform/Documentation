# Cluster Info

Genutzer Schulcluster auf http://172.17.195.1:8006/

## VM Primary Environment

## Ressourcen – Bewerbungsplattform

### Primary Server

| Ressource | Spezifikation |
|-----------|---------------|
| **Domain** | `*.application.htl-leonding.ac.at` |
| **CPUs** | 2 |
| **RAM** | 16 GB |
| **Speicher** | 120 GB |

#### Kubernetes Services

| Service | Beschreibung |
|---------|--------------|
| **Backend** | API-Backend |
| **Bewerbungsplatform** | Frontend für Bewerber |
| **Sekreteriatsplatform** | Verwaltungsoberfläche Sekretariat |
| **Abteilungsplatform** | Fachbereichs-spezifische Views |
| **Postgres DB** | Primäre Datenbank |
| **Elastic Search** | Suchindex & Logging |
| **Keycloak** | Identity & Access Management |

---

### Backup Server

| Ressource | Spezifikation |
|-----------|---------------|
| **CPUs** | 2 |
| **RAM** | 16 GB |
| **Speicher** | 50 GB |

### Nutzungsdauer
Unbegrenzt

## VM Staging Environment

### Staging Server

| Ressource | Spezifikation |
|-----------|---------------|
| **Domain** | `*.staging.application.htl-leonding.ac.at` |
| **CPUs** | 2 |
| **RAM** | 16 GB |
| **Speicher** | 120 GB |

#### Kubernetes Services

| Service | Beschreibung |
|---------|--------------|
| **Backend** | API-Backend (Staging) |
| **Bewerbungsplatform** | Frontend für Bewerber (Staging) |
| **Sekreteriatsplatform** | Verwaltungsoberfläche Sekretariat (Staging) |
| **Abteilungsplatform** | Fachbereichs-spezifische Views (Staging) |
| **Postgres DB** | Primäre Datenbank (Staging) |
| **Elastic Search** | Suchindex & Logging (Staging) |
| **Keycloak** | Identity & Access Management (Staging) |

---

### Backup Server (Staging)

| Ressource | Spezifikation |
|-----------|---------------|
| **CPUs** | 2 |
| **RAM** | 16 GB |
| **Speicher** | 90 GB |

### Nutzungsdauer
Unbegrenzt