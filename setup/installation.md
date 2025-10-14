# Installation Guide für LeoCloud und Kubernetes

Diese Anleitung beschreibt die Installation und Konfiguration von Ubuntu, LeoCloud CLI und Kubernetes (`kubectl`) auf Windows, Linux oder macOS. Sie ist für schulische Projekte optimiert und führt Schritt für Schritt durch die notwendigen Schritte.

---

## 1. Ubuntu Installation für Windows

1. Öffnen Sie die **Microsoft Store** App oder besuchen Sie die [Ubuntu Webseite](https://ubuntu.com/desktop/wsl).
2. Installieren Sie die Distribution **Ubuntu**.
3. Starten Sie Ubuntu über das Startmenü.
4. Folgen Sie den Anweisungen, um einen neuen Benutzer und ein Passwort anzulegen.

---

## 2. Installation von LeoCloud

### Linux / macOS

1. Öffnen Sie das Terminal.
2. Installieren Sie LeoCloud mit dem folgenden Befehl:

```bash
bash -c "$(curl -fsSL https://cloud.htl-leonding.ac.at/html/install.sh)"
```

3. Prüfen Sie die Installation:

```bash
leocloud --help
```
### Windows (über WSL)
1. Ubuntu starten.
2. Ubuntu aktualisieren mit:

```bash
sudo apt update && sudo apt full-upgrade
```

3. Installieren Sie LeoCloud wie unter Linux/macOS:

```bash
bash -c "$(curl -fsSL https://cloud.htl-leonding.ac.at/html/install.sh)"
```

## Installation von kubectl
Die Installation erfolgt laut offizieller Kubernetes-Dokumentation: [kubectl](https://kubernetes.io/docs/tasks/tools/)
### Linux / WSL2
1. Installieren Sie `curl`, falls nicht vorhanden
```bash
sudo apt install curl -y
```
2. Prüfen Sie die Installation:
```bash
curl --version
```
3. Laden Sie die aktuelle Version von kubectl herunter:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
4. Mache die kubectl-Binärdatei ausführbar:
```bash
chmod +x kubectl
```
5. Verschieben Sie kubectl in ein Verzeichnis, das im PATH liegt:
```bash
sudo mv kubectl /usr/local/bin/
# oder
sudo mv kubectl /usr/bin/
```
6. Prüfen Sie die Installation:
```bash
kubectl version --client
```

## LeoCloud Login & Kubernetes Konfiguration
Damit `kubectl` funktioniert, benötigen Sie eine gültige Konfigurationsdatei `(~/.kube/config)`. Falls diese bereits existiert, benennen Sie sie um oder löschen Sie sie vorher.
1. Melden Sie sich bei LeoCloud an:
```bash
leocloud auth login
```
2. Prüfen Sie die Konfiguration:
```bash
kubectl get nodes
kubectl config view
```
Der Namespace Ihres Accounts wird in der Ausgabe angezeigt.

## Erstes Deployment
Starten Sie ein Beispiel-Deployment:
```bash
leocloud get template nginx | kubectl apply -f -
```
Überprüfen Sie den Status der Pods:
```bash
kubectl get pods
```
Anzeigen des eigenen Hostnamens:
```bash
kubectl describe ingress
```
Öffnen der LeoCloud-Homepage:
```bash
leocloud get home
```

## Dashboard
Das Kubernetes Dashboard bietet eine webbasierte Benutzeroberfläche zur Verwaltung des Clusters.
1. Starten Sie das Dashboard:
```bash
leocloud dashboard
```
2. Folgen Sie den Anweisungen im Terminal.
3. Nach erfolgreichem Start können Sie das Dashboard im Browser öffnen. Passen Sie die URL auf Ihren Namespace an:
```cpp
namespace=<your-namespace>
```
Beispiel: `namespace=student-ifxxxxxx`.
