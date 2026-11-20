# Antworten zu Modul 04: Den Workflow verstehen

## Teil A: Aufbau der Workflow-Datei
1. **Ereignisse (Events):** Der Workflow startet typischerweise bei `push` (auf main/dev), `pull_request` und manuell per `workflow_dispatch`.
2. **Runner:** Er läuft auf einem Ubuntu-Runner (`ubuntu-latest`), der von GitHub bereitgestellt wird.
3. **Job und Steps:** Es gibt einen Haupt-Job (z.B. `ci`), der aus ca. 10–12 einzelnen Schritten (Steps) besteht.
4. **uses vs. run:** `uses:` nutzt vorgefertigte GitHub Actions (z.B. `actions/checkout`), während `run:` eigene Shell-Befehle (Bash) ausführt.
5. **Permissions:** Die Rechte sind minimal gehalten (z.B. `contents: read`), um die Sicherheit zu erhöhen, falls der Workflow kompromittiert wird.
6. **Template-Bedingung (`if: ${{ !github.event.repository.is_template }}`):** Verhindert, dass der Workflow direkt im Haupt-Template der Schule läuft, aber er läuft in deinem persönlichen Fork/Repo.

## Teil B: Was wird geprüft?
1. **Fehlerzuordnung:**
   - *Tabulator statt Leerzeichen:* Schlägt beim YAML-Parser (Syntaxfehler in compose.yml) fehl.
   - *html/ Ordner nicht committet:* Der Step für den Kopiervorgang oder Test schlägt fehl, da die Datei fehlt.
   - *Falscher Port (z.B. 8080 statt 8085):* Der Port-Test oder curl schlägt fehl, weil der Dienst auf dem erwarteten Port nicht erreichbar ist.
   - *compose.yml im Unterordner:* Docker Compose findet die Konfiguration im Root-Verzeichnis nicht.
2. **Warum `diff` statt nur Port-Check?** Ein Port-Check prüft nur, ob *irgendein* Dienst antwortet (selbst eine Fehlermeldung). `diff` vergleicht den echten HTML-Inhalt, um sicherzustellen, dass exakt die richtige Webseite ausgeliefert wird.
3. **Aufräumen (`if: always()`):** Sorgt dafür, dass Test-Container und Netzwerke selbst dann gelöscht werden, wenn ein Test mitten drin fehlschlägt, damit keine Ressourcen blockiert werden.

## Teil C: Einordnen
1. **CI/CD Art:** Das ist **Continuous Integration (CI)**, weil Code-Änderungen automatisiert gebaut und getestet werden. Es ist noch kein Continuous Deployment, da nichts auf einem Live-Webserver ausgespielt wird.
2. **Vorteile gegenüber manuellem Testen:** 
   - 100% reproduzierbar und fehlerfrei (kein Vergessen von Schritten).
   - Automatisch bei jedem Push – man sieht sofort, ob die Definition of Done (DoD) erfüllt ist.

## Teil D: Die DoD-Abzeichen
1. **Branch-Dev Workflow:** Läuft auf `dev`, weil dort andere Entwicklungsregeln gelten. Der Parameter `?branch=dev` im Badge zeigt gezielt den Status dieses spezifischen Branches an.
2. **GitHub-API bei Pull Requests:** Die API prüft Metadaten wie z.B. ob ein Review vorliegt, ob die Tests grün sind und ob das Vier-Augen-Prinzip eingehalten wurde.

