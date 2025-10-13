# Deployment Guide: Eigenes Projekt in die LeoCloud deployen

Dieses Dokument erklärt detailliert, wie du ein eigenes Projekt als Container in der **LeoCloud (Kubernetes)** deployen kannst. Alle Schritte sind genau beschrieben, sodass du sie einfach nachmachen kannst.

---

## Voraussetzungen

Bevor du beginnst, installiere folgende Programme:

* **Docker** – zum Bauen von Containern
* **GitHub Account + Personal Access Token** – für die Container Registry (GHCR)
* **kubectl** – Kubernetes Verwaltungstool
* **leocloud** – Zugang zur HTL LeoCloud
* **Zugriff auf LeoCloud** (Schulaccount)

### Dashboard
```bash
leocloud dashboard
```
und folgen Sie den Anweisungen im Terminal.

---

## 1: Docker Image bauen
Erstelle eine **Dockerfile** im Hauptverzeichnis deines Projekts. Beispiel für eine Angular oder React Anwendung:

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist/frontend-av /usr/share/nginx/html
EXPOSE 80
```
Wechsle in dein Projektverzeichnis und führe aus:

```bash
docker build -t ghcr.io/<github-user>/<image-name>:latest .
```

**Beispiel:**

```bash
docker build -t ghcr.io/maxmustermann/myapp:latest .
```

---

## 2: Login bei GitHub Container Registry (GHCR)

Erstelle auf GitHub einen **Personal Access Token** mit den Rechten `write:packages`.
[GitHub Token erstellen](./create-github-token.md)
Dann in GHCR einloggen:

```bash
echo "<GITHUB_TOKEN>" | docker login ghcr.io -u <github-user> --password-stdin
```

## 3: Secret erstellen
Sie benötigen ein Secret, damit Kubernetes auf das private Docker Image zugreifen kann.

Starten Sie folgenden Befehl im WSL oder Linux Terminal:
- secretname: Name des Secrets (normalerweise `grand`)
- server: ghcr.io
- your github-username: Ihr GitHub Benutzername
- GHCR-TOKEN: Ihr GitHub Token, den Sie im vorherigen Schritt erstellt haben (Wir benutzen hier den Token als Passwort, da dieser die Rechte für GHCR hat)
- github-email: Ihre GitHub Email Adresse
```bash
kubectl create secret docker-registry {{secretname}} \
--docker-server={{server}} \
--docker-username={{your github-username}} \
--docker-password={{GHCR-TOKEN}} \
--docker-email={{github-email}}
```
oder schreiben Sie ein Script, z.B. `create-secret.sh`, das Sie immer wieder verwenden können.

falls Sie bevor schon die YAML Datei deployt haben, müssen Sie diese zuerst löschen und dann wieder neu deployen, damit das neue Secret verwendet wird:
```bash
kubectl delete -f <yaml-name>.yaml
kubectl apply -f <yaml-name>.yaml 
```
löschen Sie zuerst das alte Deployment, damit das neue mit dem neuen Secret erstellt wird.

falls Sie einen alten Ingress haben oder einen Fehler bekommen, löschen Sie den alten Ingress mit:
```bash
kubectl delete ingress <ingress-name>
```


## 4: Docker Image pushen

```bash
docker push ghcr.io/<github-user>/<image-name>:latest
```

Wenn ein Fehler mit "the system cannot find the file specified." kommt, dann überprüfe ob Dockerdesktop läuft.

---

## 5: Kubernetes Deployment Datei erstellen

Erstelle die Datei **deployment.yaml** mit folgendem Inhalt:

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
          resources:
            limits:
              memory: "{{MEMORY_LIMIT}}"
              cpu: "{{CPU_LIMIT}}"
            requests:
              memory: "{{MEMORY_REQUEST}}"
              cpu: "{{CPU_REQUEST}}"
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
    - host: {{NAMESPACE}}.cloud.htl-leonding.ac.at
      http:
        paths:
          - backend:
              service:
                name: {{APP_NAME}}
                port:
                  number: 80
            path: /
            pathType: Prefix
```
Der obere Teil definiert das Deployment, der untere Teil den Ingress (Zugriff über URL).

---

## 6: Deployment starten

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl describe ingress
```

Auf deine Leocloud-Dashboard kannst du unter Services & Ingress den Status des Ingress sehen.
Im Detail siehst du unter Service den Endpunkt (ClusterIP) und den Port (normalerweise 80) und der Port soll grün sein.

---

## 7: Anwendung im Browser aufrufen

Rufe folgende URL auf:

```
https://<your-namespace>.cloud.htl-leonding.ac.at
```
