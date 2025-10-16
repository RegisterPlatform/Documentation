# Leocloud setup

1. Einführung

Dieses Dokument ist eine stark detaillierte und erweiterte Version des offiziellen LeoCloud User Manuals von Christian Aberger. Es erklärt alle Schritte ausführlich, ergänzt mit Hinweisen und Praxisbeispielen. Ideal für Einsteiger und Fortgeschrittene.

Containerisierte Anwendungen laufen zu lassen
- Kubernetes zu lernen
- Webservices zu entwickeln 
- Microservices zu testen

Wichtig: LeoCloud ist keine Entwicklungsumgebung. Zum Entwickeln und Debuggen ist zuerst minikube lokal zu verwenden.

2. Voraussetzungen
- HTL-Leonding Account
- Terminal (Linux/Mac) oder WSL2/Ubuntu (Windows)
- Tools: leocloud, kubectl

3. [Installation und Erstes Deployment](./installation.md)
4. [Deployment von Anwendungen](./deployment.md)

5. Die YAML-Dateien der RegisterPlattform werden in folgender Reihenfolge deployed:
- secrets
- configmaps
- claims
- postgres
- keycloak-postgres
- keycloak
- elasticsearch
- backend
- frontend