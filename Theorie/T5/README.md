# Continuous Deployment

---

## 1) Was ist Continuous Deployment?
Continuous Deployment bedeutet:  
Jede Code-Änderung, die alle Tests und Checks besteht, wird automatisch in Produktion deployt – ohne manuelle Freigabe.

Typischer Ablauf:
Commit → Build → Tests → Security/Quality Checks → Deploy → Monitoring

---

## 2) Unterschied: Continuous Delivery vs Continuous Deployment

- Continuous Delivery  
  Software ist jederzeit bereit für Produktion, aber der letzte Schritt ist manuell (Freigabe).

- Continuous Deployment  
  Kein manueller Schritt – alles, was grün ist, geht automatisch live.

Merksatz:  
Delivery = bereit zum Release 
Deployment = Release passiert automatisch

---

## 3) Vor- und Nachteile

### Continuous Delivery
**Vorteile**
- Mehr Kontrolle über Release-Zeitpunkt
- Weniger Risiko für sensible Systeme

**Nachteile**
- Manueller Schritt kann Releases verzögern

### Continuous Deployment
**Vorteile**
- Sehr schnelle Releases
- Kleine Änderungen → leichter zu fixen
- Sehr gut für agile Teams

**Nachteile**
- Sehr gute Tests & Monitoring nötig
- Fehler können direkt in Prod landen

---

## 4) Deployment-Strategien

### Blue/Green Deployment
- Zwei Umgebungen: Blue (alt) und Green (neu)
- Traffic wird komplett auf die neue Version umgeschaltet
- Rollback = sofort zurück

Pro: Schneller Rollback, kein Downtime  
Contra: Mehr Ressourcen nötig

---

### Canary Deployment
- Neue Version bekommt zuerst nur wenige User
- Wenn alles stabil ist → mehr Traffic
- Bei Problemen → abbrechen

Pro: Geringes Risiko  
Contra: Mehr Setup & Monitoring nötig

---

## 5) Was ist A/B Testing?
- User werden zufällig auf Variante A oder B verteilt
- Ziel: herausfinden, welche Version besser funktioniert
- Fokus: Business-/UX-Ergebnis, nicht Stabilität

---

## 6) Was sind Feature Toggles?
- Schalter im Code, um Features ein/aus zu schalten
- Ermöglichen Deploys ohne Feature direkt sichtbar zu machen
- Wichtig für Continuous Deployment

---

## 7) Rollback-Strategien
- Traffic zurückschalten (Blue/Green)
- Canary stoppen
- Alte Version neu deployen
- Feature Toggle deaktivieren (sehr schnell)

---

## 8) Continuous Monitoring
- Dauerhafte Überwachung von Systemen
- Beobachtet z.B.:
  - Fehler
  - Antwortzeiten
  - Systemlast
- Mit Alerts bei Problemen

---

## 9) Sichere Passwort-Speicherung
- Nie Klartext speichern
- Passwörter hashen, z.B. mit:
  - Argon2
  - bcrypt
- Immer mit Salt
- Secrets nicht im Code, sondern z.B. in:
  - Vault
  - Environment Variablen

---

## 10) Arten von Deployment (Beispiele)

| Art               | Benötigte Software             |
| ----------------- | ------------------------------ |
| Direkt auf Server | OS, Runtime (Java, Node, etc.) |
| Docker Container  | Docker                         |
| Docker Swarm      | Docker, Swarm                  |
| Kubernetes        | Docker, Kubernetes             |
| Cloud PaaS        | AWS, Azure, GCP                |
| Serverless        | AWS Lambda, Azure Functions    |
