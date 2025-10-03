# Projektübersicht

## Technologien
- **Frontend:** Angular mit TypeScript
- **Backend:** C# mit EF Core
- **Datenbank:** PostgreSQL

---  

## Funktionen
- **Sek.:** Die eingegebenen Daten werden auf Richtigkeit und Vollständigkeit geprüft.
- **AV:** Die Schüler werden anhand definierter Kriterien den passenden Klassen zugeteilt und anschließend übersichtlich organisiert.

---  

## Dateien
- Dateien werden nicht in der Datenbank, sondern in einem Ordner gespeichert.
- Speicherung erfolgt über die **Keycloak-ID**.
- Dateien werden ohne Erweiterungen, dafür mit **Magic Number** gespeichert.

**Geplante Funktionen:**
- Bewerbungen abrufen
- (neuen **RoleController** erstellen)

---  

## Leocloud
- **Ingress:** Kontrolle von Ein- und Ausgängen
- **Claim (Keycloak, Backend):** Fester Speicherplatz, um Datenverlust zu vermeiden


## Backend

- **API**
    - ASP.NET Core (MVC)
    - Controller mit API-Versionierung
    - Swagger API mit Keycloak

- **App Settings**
    - Keycloak
    - API-Versionierung
    - Mail-Server
    - Allgemeine Konfigurationen

- **E-Mail**
    - HTML-basiert
    - Nutzung eines MailService anstelle direkter Implementierung

- **Models & Services**
    - Dateien werden mit AES verschlüsselt
    - Schlüssel in den App Settings gespeichert

- **Migrationen**
    - Durchführung bei Änderungen an den Models

- **DataContext**
    - Konfiguration der Datenbank
    - Create & Update & IDs automatisch generiert

- **Keycloak**
    - Berechtigungen abrufen
    - Benutzer anlegen
    - Rollen zuweisen
---

## GitHub
- **Issues:** Erfassen neuer oder korrigierter logischer Funktionalitäten
- **Branches:** Für jede Issue eine eigene Branch erstellen
- **Commits:** Immer *amend commit* verwenden
- **Organisation:** Nutzung von Project Board und Backlog

---  

## Kommunikationsweg
- **Discord**

---  

## Struktur
![Struktur](./registerplatformrebuilt_staging.png)