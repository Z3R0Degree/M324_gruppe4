# Continuous Integration – Kurzfassung der Themen

## 1. Was ist Continuous Integration (CI) und wie wird es umgesetzt?

**Definition:**
Continuous Integration bedeutet, dass Entwickler ihren Code **regelmässig** ins zentrale Repository integrieren. Jede Änderung löst **automatische Builds und Tests** aus. Ziel ist eine stabile Codebasis und frühes Erkennen von Fehlern.

**Bedeutung:**

* Schnelles Feedback
* Weniger Integrationsprobleme
* Basis für CI/CD und moderne DevOps-Prozesse

**Technische Umsetzung:**

* CI-Server: GitLab CI, GitHub Actions, Jenkins, Azure DevOps
* Automatisierte Schritte:

  * Build
  * Tests
  * Code-Analyse (Linting, Security-Scans)
  * Artefakt-Erstellung

**Rolle im Team:**

* Gemeinsame, immer stabile Hauptbranch
* Transparentes Testing
* Schnellere Zusammenarbeit durch klare Pipeline-Regeln

---

## 2. Vor- und Nachteile von CI

**Vorteile:**

* Frühzeitige Fehlererkennung
* Höhere Produktqualität
* Stabilere Releases
* Weniger Merge-Konflikte
* Schnelleres Arbeiten dank Feedback

**Nachteile / Herausforderungen:**

* Aufwand für Pipelines & Testautomatisierung
* Flaky Tests können Prozesse blockieren
* Hoher Ressourcenverbrauch (viele Builds)

**Langfristiger Einfluss:**

* Bessere Codequalität
* Schnellere Releasezyklen
* Effizientere Teamarbeit

---

## 3. Was ist Continuous Testing?

**Definition:**
Durchgehendes, automatisiertes Testen während des gesamten Entwicklungsprozesses – nicht erst am Ende.

**Unterschied zu früher:**

* Früher: Testphase nach der Entwicklung
* Heute: Tests nach jedem Commit / Build

**Rolle im Prozess:**

* Liefert Risikoeinschätzung für Releases
* Machen CI/CD überhaupt erst möglich

**Arten von automatisierten Tests:**

* Unit Tests
* Integrationstests
* API Tests
* E2E Tests
* Performance/Security-Checks

---

## 4. Branching-Strategien (inkl. Trunk-Based)

**Warum wichtig?**
Branching strukturiert Code, Teamaufteilung und Release-Strategien.

**Bekannte Strategien:**

* **GitFlow:** viele Branches, geeignet für Releases
* **Feature Branches:** klassisch für einzelne Aufgaben
* **Trunk-Based Development:**

  * Alle entwickeln fast ausschliesslich auf *main/trunk*
  * Sehr kurze Branches
  * Ideal für CI/CD, schnelle Releases

**Unterschiede:**

* GitFlow = Kontrolle & Komplexität
* Trunk-Based = Geschwindigkeit & Einfachheit

---

## 5. Commits und Branches mit User Stories verknüpfen

**Warum sinnvoll?**

* Nachvollziehbarkeit: „Welcher Code gehört zu welcher Aufgabe?“
* Vereinfachtes Debugging
* Transparenz im Team

**Best Practices:**

* Branch-Namen: `feature/US-123-login-system`
* Commit Messages: `US-123 Fix password validation`
* Tools verlinken Stories automatisch (GitLab, Azure Boards, Jira)

---

## 6. Merge-Strategien

**1. Merge Commit**

* Historie bleibt vollständig
* Gut für Teams mit vielen Parallel-Branches

**2. Squash Merge**

* Alle Commits werden zu einem zusammengefasst
* Saubere, lineare Historie

**3. Rebase**

* Commits „umgeschrieben“ auf neuen Stand
* Sehr saubere Historie, aber riskanter

**Wann benutzen?**

* Feature Branch → Squash
* Hotfix → Merge Commit
* Kleine Teams → Rebase möglich

---

## 7. Semantic Versioning

**Zweck:**
Klare Kommunikation von Änderungen und Kompatibilität.

**Schema:**

```
MAJOR.MINOR.PATCH
```

**Bedeutung:**

* **MAJOR:** Breaking changes
* **MINOR:** Neue Features, kompatibel
* **PATCH:** Bugfixes

---

## 8. Mono- vs. Multirepo bei Microservices

| Ansatz        | Vorteile                                      | Nachteile                             |
| ------------- | --------------------------------------------- | ------------------------------------- |
| **Monorepo**  | Einfachere Abhängigkeiten, einheitliche Tools | Sehr grosses Repo, mehr Konflikte     |
| **Multirepo** | Teams arbeiten unabhängig                     | Schwieriger bei gemeinsamen Libraries |

**Im Microservice-Kontext:**

* Multirepo → mehr Autonomie
* Monorepo → bessere Übersicht, aber schwer skalierbar

---

## 9. Artifact Repository

**Definition:**
Ort zur Speicherung von Build-Artefakten wie JARs, Docker-Images, Packages.

**Beispiele:**

* Docker Registry
* Nexus Repository
* Artifactory
* GitLab Package Registry

**Warum wichtig?**

* Reproduzierbare Builds
* Zentrale Versionierung
* Unveränderbare Release-Artefakte
* Pflichtbestandteil jedes CI/CD-Prozesses


# 📚 Quellen


* Fowler, M. (2006). *Continuous Integration*. [https://martinfowler.com/articles/continuousIntegration.html](https://martinfowler.com/articles/continuousIntegration.html)
* Atlassian. *Continuous Integration & Testing*. [https://www.atlassian.com/continuous-delivery](https://www.atlassian.com/continuous-delivery)
* Red Hat. *What is CI/CD?* [https://www.redhat.com/en/topics/devops/what-is-ci-cd](https://www.redhat.com/en/topics/devops/what-is-ci-cd)
* Atlassian. *Git Workflows*. [https://www.atlassian.com/git/tutorials/comparing-workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)
* TrunkBasedDevelopment.com. *Official Guide*. [https://trunkbaseddevelopment.com](https://trunkbaseddevelopment.com)
* GitLab. *GitLab Flow & Issues*. [https://docs.gitlab.com](https://docs.gitlab.com)
* Atlassian. *Git Merge Strategies*. [https://www.atlassian.com/git](https://www.atlassian.com/git)
* Preston-Werner, T. (2013). *Semantic Versioning 2.0.0*. [https://semver.org](https://semver.org)
* GitLab. *Monorepo vs Multi-Repo*. [https://about.gitlab.com/blog](https://about.gitlab.com/blog)
* Google Research. *Why Google Uses a Monorepo*. [https://research.google](https://research.google)
