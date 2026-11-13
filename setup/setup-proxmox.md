# Setup Proxmox and Kubernetes

- Proxmox VM vom verantwortlichen Lehrer einholen
- Domain von Systemadministrator setzen lassen um nach aussen sichtbar zu sein
- Kubernetes aufsetzen mit den dazu verwendeten Skripte im Deployment Repo
    - [k8s Setup](https://github.com/RegisterPlatform/Deployment/blob/main/shell/k8s-setup.sh)
    - [Lets Encrypt Setup](https://github.com/RegisterPlatform/Deployment/blob/main/shell/lets-encrypt.sh)
    - [Tailscale](https://github.com/RegisterPlatform/Deployment/blob/main/shell/tailscale.sh)
    - Reihenfolge von oben einhalten. (Wichtig)
- keycloak.yaml einpsielen und applien auf der Proxmox VM mit `kubectl apply -f keycloak.yaml`
- register.yaml einspielen und applien
- frontend.yaml einspielen und applien
- DNS Challenge abwarten, um Letsencrypt einrichten zu lassen für die jeweiligen Wildcard Einträge
- Kaffee trinken und warten
- Logs checken ob alle gut läuft und von außen testen