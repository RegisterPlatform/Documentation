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

Wechsle in dein Projektverzeichnis und führe aus:

```bash
docker build -t ghcr.io/<github-user>/<image-name>:v1 .
```

**Beispiel:**

```bash
docker build -t ghcr.io/maxmustermann/myapp:v1 .
```

---

## 2: Login bei GitHub Container Registry (GHCR)

Erstelle auf GitHub einen **Personal Access Token** mit den Rechten `write:packages`.
[GitHub Token erstellen](./create-github-token.md)
Dann in GHCR einloggen:

```bash
echo "<GITHUB_TOKEN>" | docker login ghcr.io -u <github-user> --password-stdin
```

---

## 3: Docker Image pushen

```bash
docker push ghcr.io/<github-user>/<image-name>:v1
```

---

## 4: Kubernetes Deployment Datei erstellen

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
              cpu: "{{CPU_REQUEST}}
---
apiVersion: v1
kind: Service
metadata:
  name: {{APP_NAME}}-service
spec:
  selector:
    app: {{APP_NAME}}
  ports:
    - port: {{SERVICE_PORT}}
      targetPort: {{CONTAINER_PORT}}
      protocol: TCP
  type: ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{APP_NAME}}-ingress
spec:
  rules:
    - host: {{NAMESPACE}}.cloud.htl-leonding.ac.at
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{APP_NAME}}-service
                port:
                  number: {{SERVICE_PORT}}
```

---

## 6: Deployment starten

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
