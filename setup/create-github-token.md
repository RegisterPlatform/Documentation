# GitHub Token erstellen
## 1. Zu den GitHub Token Einstellungen gehen

1. Öffne GitHub: https://github.com

2. Rechts oben auf dein Profilbild klicken → Settings

3. Links unten Developer settings anklicken

4. Dann Personal access tokens → Tokens (classic)

5. Auf Generate new token klicken → Generate new token (classic)

## 2. Token konfigurieren
Note (Beschreibung): LeoCloud Docker Zugriff

Expiration: 90 Tage oder weniger (Sicherheit!)

Scopes auswählen (GANZ WICHTIG): 
- read:packages – Images herunterladen
- write:packages – Images hochladen
- Optional: delete:packages – Images löschen
- Optional: repo – nur falls du auf private Repositories zugreifen musst

## 3. Token generieren und kopieren
1. Auf Generate token klicken
2. Den generierten Token kopieren und sicher aufbewahren (wird nur einmal angezeigt!)
3. Token im Terminal verwenden zum Login bei GHCR (siehe Schritt 2 im [Deployment Guide](./deployment.md))