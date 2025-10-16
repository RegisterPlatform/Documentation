# Leocloud setup

## 1. Einführung

Dieses Dokument ist eine ausführlich erweiterte Version des offiziellen LeoCloud User Manuals von Christian Aberger. Es beschreibt alle notwendigen Schritte detailliert, ergänzt durch Hinweise aus der Praxis und Beispiele. Es eignet sich sowohl für Einsteiger als auch für fortgeschrittene Anwender.

LeoCloud eignet sich besonders für:
- das Ausführen containerisierter Anwendungen
- das Erlernen von Kubernetes
- die Entwicklung von Webservices
- das Testen von Microservices

Wichtig: LeoCloud ist keine Entwicklungsumgebung. Für die Entwicklung und das Debugging sollte zuerst Minikube lokal verwendet werden. Erst nach erfolgreichem Testen erfolgt der Upload auf LeoCloud.

## 2. Voraussetzungen
- gültiger HTL-Leonding Account
- Terminal unter Linux/Mac oder WSL2/Ubuntu unter Windows
- installierte Tools:
  - `leocloud`
  - `kubectl`

## 3. [Installation und Erstes Deployment](./installation.md)
## 4. [Deployment von Anwendungen](./deployment.md)

## 5. Reihenfolge der YAML-Deployments für die Register-Plattform:
- `secrets`
- `configmaps`
- `claims`
- `postgres`
- `keycloak-postgres`
- `keycloak`
- `elasticsearch`
- `backend`
- `frontend`