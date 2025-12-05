# DevOps Prozesse

# 1. Was ist SDLC?

Der Software Development Life Cycle (SDLC) ist ein strukturierter Prozess zur Planung, Entwicklung, Testung und Auslieferung von Software. Er stellt sicher, dass Software qualitativ hochwertig, kosteneffizient und innerhalb der gesetzten Anforderungen** entsteht.

### Ziel des SDLC

* Risiken minimieren
* Qualität sichern
* Transparente Projektstruktur
* Klare Verantwortlichkeiten
* Planbare Kosten und Zeitrahmen

---

## 1.1 Phasen des SDLC

### SDLC Ablauf

![SDLC cycle](./images/what-are-the-stages-of-the-sdlc.avif)

```
[ Planning ] → [ Requirements gathering ] → [ Design + Prototyping ] → [ Development ] → [ Testing ] → [ Deployment ] → [ Operations + Maintenance ]
```

### **1. Planning (Projektplanung)**

* Ziele festlegen
* Ressourcen, Budget, Zeitplan
* Risiken identifizieren

---

### **2. Requirements Analysis (Anforderungsanalyse)**

* Funktionale & nicht-funktionale Anforderungen
* Gespräche mit Stakeholdern
* Dokumentation & Abnahmekriterien

---

### **3. System Design (Systementwurf)**

* Technisches Design & Architektur
* Datenmodelle, Schnittstellen
* Erste Prototypen

---

### **4. Development (Entwicklung)**

* Programmierung
* Versionsverwaltung (Git)
* Code Reviews & interne Tests

---

### **5. Testing (Qualitätssicherung)**

* Unit-, Integrations- & Systemtests
* Fehlerbehebung
* Qualitätsprüfung

---

### **6. Deployment**

* Rollout in Staging/Produktion
* Automatisierte Deployments
* Übergabe an Betrieb

---

### **7. Operations & Maintenance (Wartung)**

* Fehlerbehebung & Updates
* Monitoring & Optimierung
* Laufende Weiterentwicklung


## 1.2 Anwendung des SDLC im Projekt

In klassischen Projekten (z. B. Wasserfallmodell) wird der SDLC **linear** durchlaufen → jede Phase ist abgeschlossen, bevor die nächste beginnt.

In modernen Projekten (Scrum, Agile):

* SDLC wird iterativ angewendet
* Jede Iteration (Sprint) beinhaltet eigene Mini-SDLC-Zyklen
* Erlaubt schnelle Anpassungen

---

# 2. Was ist der DevOps Lifecycle?

DevOps ist ein Ansatz, der Development (Dev) und Operations (Ops) vereint. Ziel ist es, Software schneller, zuverlässiger und kontinuierlich bereitzustellen.

## 2.1 Ziele von DevOps

* Automatisierung der Prozesse
* Kontinuierliche Verbesserung
* Kurze Release-Zyklen
* Enge Zusammenarbeit zwischen Entwicklern und Betrieb

Hier ist der angepasste Abschnitt, sodass er exakt deinem neuen Ablauf entspricht:

---

## 2.2 DevOps Lifecycle Phasen

![DevOps cycle](./images/devops-infinity-loop.png)


```
Plan → Build → Test → Deploy → Operate → Observe → Continuous feedback → Discover → (back to Plan)
```

### **1. Plan**

* Anforderungen, Backlog Items, Architekturideen
* Priorisierung der Ziele
* Definition von Akzeptanzkriterien

### **2. Build**

* Entwicklung der Software (Coding)
* Versionsverwaltung (Git)
* Erstellung der Build-Artefakte
* Statische Codeanalyse (optional)

### **3. Test**

* Automatisierte Tests (Unit, Integration, End-to-End)
* Qualitätssicherung und Fehlererkennung
* Testreports zur schnellen Fehleranalyse

### **4. Deploy**

* Continuous Deployment / Delivery
* Infrastruktur-Automatisierung (Kubernetes, Docker, Terraform)
* Deployment in Test-, Staging- oder Produktionsumgebungen

### **5. Operate**

* Betrieb der Anwendung im Live-System
* Ressourcenverwaltung (Compute, Storage, Network)
* Skalierung (horizontal/vertikal)

### **6. Observe**

* Monitoring (z. B. Prometheus, Grafana)
* Logging (ELK Stack, Loki, CloudWatch)
* Telemetriedaten sammeln (Performance, Fehler, Traffic)

### **7. Continuous Feedback**

* Sammeln von Nutzerfeedback
* Analyse von Monitoring-Daten
* Identifikation von Verbesserungspotenzial
* Rückfluss des Feedbacks in das Backlog

### **8. Discover**

* Neue Anforderungen erkennen
* Markt- und Nutzerforschung
* Hypothesen für neue Features entwickeln
* Vorbereitung für den nächsten Plan-Zyklus

---

# 3. Unterschiede zwischen SDLC und DevOps Lifecycle

| Bereich              | SDLC                       | DevOps                             |
| -------------------- | -------------------------- | ---------------------------------- |
| **Ansatz**           | Linear / Phasenbasiert     | Kontinuierlich, zyklisch           |
| **Teamstruktur**     | Dev & Ops getrennt         | Dev & Ops integriert               |
| **Ziel**             | Stabile, geplante Releases | Schnelle, kontinuierliche Releases |
| **Testing**          | Eigenständige Phase        | Automatisiert & kontinuierlich     |
| **Feedback**         | Spät (nach Entwicklung)    | Früh und kontinuierlich            |
| **Automatisierung**  | Gering                     | Sehr hoch                          |
| **Release-Frequenz** | selten (Wochen/Monate)     | häufig (täglich/stündlich)         |

---

# 4. Was ist ein MVP und welche Bedeutung hat es im DevOps Lifecycle?

## 4.1 Definition MVP

Ein Minimum Viable Product (MVP) ist die kleinste funktionierende Version einer Software, die genug Funktionen enthält, um:

* echten Nutzen zu bieten
* Feedback von Nutzern zu sammeln

### Kernmerkmale eines MVP

* Minimaler Funktionsumfang
* Schnell entwickelbar
* Grundlage für Feedback
* Fokus auf Lernen statt Perfektion

---

## 4.2 Rolle des MVP im DevOps Lifecycle

### **Im Plan-Schritt**

* MVP definiert, welche Funktionen *wirklich notwendig* sind
* Priorisierung des Product Backlogs

### **Im Code- und Build-Schritt**

* Entwicklungsaufwand ist kleiner → schneller implementierbar

### **Im Test-Schritt**

* Erlaubt frühe Validierung der Hypothesen

### **Im Release- und Deploy-Schritt**

* Durch DevOps-Automatisierung kann ein MVP schnell veröffentlicht werden

### **Im Operate- und Monitor-Schritt**

* Nutzerfeedback ist entscheidend → echte Daten aus Produktion
* Feedback fließt in nächste Iterationen ein

### Warum ist ein MVP wichtig?

* Risikoreduktion
* Kürzere Time-to-Market
* Fokus auf Kundenbedürfnisse
* Basis für kontinuierliche Verbesserung (DevOps-Kernprinzip)

---

# Quellen

### SDLC:

* Sommerville, I. (2016). *Software Engineering*. Pearson.
* Pressman, R. (2014). *Software Engineering: A Practitioner’s Approach*.
* IBM Documentation: [https://www.ibm.com/topics/sdlc](https://www.ibm.com/topics/sdlc)
* Atlassian SDLC Guide: [https://www.atlassian.com/software-development](https://www.atlassian.com/software-development)

### DevOps:

* Microsoft Azure DevOps Docs: [https://learn.microsoft.com/devops](https://learn.microsoft.com/devops)
* AWS DevOps Introduction: [https://aws.amazon.com/devops/](https://aws.amazon.com/devops/)
* Atlassian DevOps Guide: [https://www.atlassian.com/devops](https://www.atlassian.com/devops)

### MVP:

* Ries, E. (2011). *The Lean Startup*.
* ProductPlan MVP Definition: [https://www.productplan.com/glossary/mvp](https://www.productplan.com/glossary/mvp)
* Scrum.org – MVP Explanation
