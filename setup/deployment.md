# Deployment Guide: Eigenes Projekt in die LeoCloud deployen

Diese Anleitung erklärt Schritt für Schritt, wie du ein eigenes Projekt als Docker-Container in der LeoCloud (Kubernetes) deployen kannst. Alle Schritte sind klar beschrieben und einfach nachvollziehbar.

---

## Voraussetzungen

Bevor du beginnst, installiere folgende Programme:

| Programm                               | Zweck                                |
|----------------------------------------|--------------------------------------|
| Docker                                 | Zum Bauen von Containern             |
| GitHub Account + Personal Access Token | Für GitHub Container Registry (GHCR) |
| kubectl                                | Verwaltung von Kubernetes            |
| leocloud CLI                           | Zugang zur HTL LeoCloud              |
| LeoCloud Zugang                        | Deployment in der Schul-Cloud        |

### LeoCloud Dashboard öffnen
```bash
leocloud dashboard
```
Folge den Anweisungen im Terminal zur Anmeldung.

---

## 1: Docker Image bauen

Erstelle im Hauptverzeichnis deines Projekts eine Datei namens **Dockerfile**. Beispiel für eine Angular- oder React-Anwendung:

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --configuration=production

FROM nginx:alpine
COPY --from=build /app/dist/$APP_NAME/browser/ /usr/share/nginx/html
EXPOSE 80
```

Prüfe, ob die Pfade und Befehle zu deinem Projekt passen:
```bash
docker build .
```

Baue dein Docker Image:
```bash
docker build -t ghcr.io/<github-user>/<image-name>:latest .
```

**Beispiel:**

```bash
docker build -t ghcr.io/maxmustermann/myapp:latest .
```

---

## 2: Login bei GitHub Container Registry (GHCR)

Erstelle auf GitHub einen Personal Access Token mit der Berechtigung `write:packages` und logge dich in GHCR ein:
[GitHub Token erstellen](./create-github-token.md)

```bash
echo "<GITHUB_TOKEN>" | docker login ghcr.io -u <github-user> --password-stdin
```

---

## 3: Kubernetes Secret erstellen

Ein Secret wird benötigt, damit Kubernetes dein privates Image aus GHCR ziehen kann:

```bash
kubectl create secret docker-registry <secret-name> \
  --docker-server=ghcr.io \
  --docker-username=<github-user> \
  --docker-password=<GITHUB_TOKEN> \
  --docker-email=<github-email>
```
oder schreiben Sie ein Script, z.B. `create-secret.sh`, das Sie immer wieder verwenden können.

Falls du das Deployment bereits vorher erstellt hast, musst du es neu anwenden:

```bash
kubectl delete -f <yaml-name>.yaml
kubectl apply -f <yaml-name>.yaml 
```

Falls ein alter Ingress Probleme verursacht:

```bash
kubectl delete ingress <ingress-name>
```

---

## 4: Docker Image pushen

```bash
docker push ghcr.io/<github-user>/<image-name>:latest
```

Wenn ein Fehler mit "the system cannot find the file specified." kommt, dann überprüfe ob Dockerdesktop läuft.

---

## 5: Deployment-Datei erstellen

Erstelle die Datei `deployment.yaml` mit folgendem Inhalt:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{APP_NAME}}
spec:
  replicas: {{REPLICAS}}
  selector:
    matchLabels:
      app: {{APP_NAME}}
  template:
    metadata:
      labels:
        app: {{APP_NAME}}
    spec:
      imagePullSecrets:
        - name: {{IMAGE_PULL_SECRET}}
      containers:
        - name: {{APP_NAME}}
          image: ghcr.io/{{GITHUB_USER}}/{{IMAGE_NAME}}:{{IMAGE_TAG}}
          ports:
            - containerPort: {{CONTAINER_PORT}}
---
apiVersion: v1
kind: Service
metadata:
  name: {{APP_NAME}}
spec:
  selector:
    app: {{APP_NAME}}
  ports:
    - protocol: TCP
      port: 80
      targetPort: {{CONTAINER_PORT}}
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{APP_NAME}}-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  ingressClassName: nginx
  rules:
    - host: {{NAMESPACE}}.cloud.htl-leonding.ac.at
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{APP_NAME}}
                port:
                  number: 80
```

---

## 6: Deployment starten
Vorbereitung:
- Terminal in das Projektverzeichnis wechseln (wo die `deployment.yaml` liegt)
- Linux/WSL2 verwenden
- Docker Desktop starten

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl describe ingress
```

---

## 7: Anwendung im Browser aufrufen

Rufe folgende URL auf:

```
https://<your-namespace>.cloud.htl-leonding.ac.at
```

## Update Deployment
Wenn du dein Image aktualisieren möchtest:
1. Baue das Docker Image neu (Schritt 1)
2. Pushe das Image zu GHCR (Schritt 4)
3. Aktualisiere das Deployment in der LeoCloud:
```bash
kubectl rollout restart deployment <APP_NAME>
```
