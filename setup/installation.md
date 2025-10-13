# Installation Guide

## Ubuntu Installation für Windows
1. Öffne die "Microsoft Store" App oder die [Ubuntu](https://ubuntu.com/desktop/wsl) Webseite.
2. Installiere "Ubuntu"
3. Starte Ubuntu über das Startmenü.
4. Folge den Anweisungen, um einen neuen Benutzer und ein Passwort zu erstellen.


## leocloud Installation
### Linux / OSX
1. Öffne dein Terminal und installiere leocloud mit:
```bash
bash -c "$(curl -fsSL https://cloud.htl-leonding.ac.at/html/install.sh)"
```
2. Testen der Installation mit:
```bash
leocloud --help
```
### Windows
1. Ubuntu starten.
2. Ubuntu aktualisieren mit:
```bash
sudo apt update && sudo apt full-upgrade
```
3. leocloud installieren mit:
```bash
bash -c "$(curl -fsSL https://cloud.htl-leonding.ac.at/html/install.sh)"
```

## kubectl Installation
Die Installation von kubectl erfolgt laut offizieller Kubernetes-Dokumentation: [kubectl](https://kubernetes.io/docs/tasks/tools/)
### Linux / WSL2
1. Installiere curl, falls noch nicht installiert:
```bash
sudo apt install curl -y
```
2. Verifiziere die Installation mit:
```bash
curl --version
```
3. Öffne dein Terminal und lade die neueste Version von kubectl herunter:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
4. Mache die kubectl-Binärdatei ausführbar:
```bash
chmod +x kubectl
```
5. Verschiebe die Binärdatei in ein Verzeichnis, das in deinem PATH liegt(falls kubectl nicht in bin ist):
```bash
sudo mv kubectl /usr/local/bin/
oder
sudo mv kubectl /usr/bin/
```

### OSX
1. Öffne dein Terminal und installiere kubectl mit Homebrew:
```bash
brew install kubectl
```
2. Teste die Installation mit:
```bash
kubectl version --client --output=yaml
```

### Test der Installation
Teste die Installation mit:
```bash
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```
Wenn die Ausgabe `kubectl: OK` ist, ist die Installation erfolgreich.


## LeoCloud Login & Kubernetes Config
Damit kubectl funktioniert brauchen wir eine gültige Konfigurationsdatei. Diese wird als ~/.kube/config gespeichert. Sollte diese Datei bereits existieren, bennen Sie diese um oder löschen Sie sie vorher.
1. Melde dich bei LeoCloud an mit:
```bash
leocloud auth login
```
2. Prüfe ob die Konfigurationsdatei erstellt wurde mit:
```bash
kubectl get nodes
kubectl config view
```
In der Ausgabe des letzten Kommandos sehen Sie den Namen Ihres Namespaces.

## Erstes Deployment
Deployment starten mit:
```bash
leocloud get template nginx | kubectl apply -f -
```
Sie können den Status des Deployments mit folgendem Befehl überprüfen:
```bash
kubectl get pods
```
eigener Hostname anzeigen lassen mit:
```bash
kubectl describe ingress
```
Leocloud Homepage öffnen mit:
```bash
leocloud get home
```

## Dashboard
Das Kubernetes Dashboard ist eine webbasierte Benutzeroberfläche, die es ermöglicht, Kubernetes-Cluster zu verwalten und zu überwachen. Es bietet eine visuelle Darstellung der Ressourcen im Cluster und ermöglicht es Benutzern, verschiedene Aufgaben durchzuführen, wie z.B. das Erstellen, Aktualisieren und Löschen von Ressourcen.
1. Starte das Dashboard mit:
```bash
leocloud dashboard
```
und folgen Sie den Anweisungen im Terminal.