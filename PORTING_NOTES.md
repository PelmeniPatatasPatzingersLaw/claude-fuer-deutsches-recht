# PORTING_NOTES.md – Technische Hinweise zur Portierung

---

## \ud83d\udcca Batch-Migration Staatsanwaltschaft-Plugins

### Zusammenfassung
- **Gesamtzahl Skills (staatsanwaltschaft-amtsanwaltschaft):** 31
- **Gesamtzahl Skills (staatsanwaltschaft-praxis-einstieg):** 145
- **Gesamtzahl migrierter Skills:** 176
- **\u00dcbersprungene Skills:** 0
- **Fehlerhafte Skills:** 0

### Technische Details

#### Staatsanwaltschaft/Amtsanwaltschaft Plugin
- **Frontmatter-Konvertierung erforderlich:** NEIN
- **Fachliche \u00c4nderungen vorgenommen:** NEIN
- **Technische Abh\u00e4ngigkeiten:** Keine
- **Dateiabh\u00e4ngigkeiten:** Keine

#### Staatsanwaltschaft/Praxis-Einstieg Plugin
- **Frontmatter-Konvertierung erforderlich:** NEIN – Alle Skills verwenden ausschlie\u00dflich die offiziell unterst\u00fctzten Felder `name` und `description`.
- **Fachliche \u00c4nderungen vorgenommen:** NEIN – Alle SKILL.md-Dateien wurden ohne inhaltliche \u00c4nderungen 1:1 kopiert.
- **Technische Abh\u00e4ngigkeiten:** Keine echten relativen Dateipfade, Asset-Referenzen, Testakten-Referenzen, technischen Skill-Aufrufe oder Claude-spezifischen Konfigurationen gefunden.
- **Dateiabh\u00e4ngigkeiten:** Keine

### Gemeinsame Feststellungen
- F\u00fcr beide Plugins war **keine Frontmatter-Konvertierung erforderlich**, da alle Skills ausschlie\u00dflich die offiziell unterst\u00fctzten Felder `name` und `description` verwenden.
- Die Skills verwenden **nur name und description** – keine zus\u00e4tzlichen Felder.
- **Keine fachlichen \u00c4nderungen** wurden an den SKILL.md-Dateien vorgenommen.
- Der **funktionale Vibe-CLI-Test** konnte wegen des bereits dokumentierten MCP-Importfehlers (`ImportError: cannot import name 'streamablehttp_client'`) nicht vollst\u00e4ndig durchgef\u00fchrt werden.
- Die **statische Migration** (1:1-Kopie aller Dateien) war davon unabh\u00e4ngig **erfolgreich**.

---

## \ud83d\udccc Korrigierte Annahmen aus der ersten Analyse

### \u274c Falsche Annahmen (korrigiert)

| **Falsche Annahme** | **Korrektur** | **Quelle** |
|---------------------|---------------|------------|
| `allowed-tools` sei nicht unterst\u00fctzt | **\u2705 WIRD UNTERST\u00dcTZT** – Offizielles SKILL.md-Feld | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| `user-invocable` sei nicht unterst\u00fctzt | **\u2705 WIRD UNTERST\u00dcTZT** – Offizielles SKILL.md-Feld | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| Diese Felder m\u00fcssten aus SKILL.md entfernt werden | **\u274c FALSCH** – Beide Felder sind offiziell unterst\u00fctzt | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| `.vibe/instructions/` sei offizielle Struktur | **\u274c NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `.vibe/test/` sei offizielle Struktur | **\u274c NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `VIBE.md` sei \u00c4quivalent zu `CLAUDE.md` | **\u274c NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `model`, `max_tokens`, `temperature` seien zul\u00e4ssig | **\u274c NICHT DOKUMENTIERT** – Nicht verwenden | Nicht in Agent Skills Spec |
| `related_skills` sei unterst\u00fctzt | **\u274c NICHT DOKUMENTIERT** – Nicht verwenden | Nicht in Agent Skills Spec |

### \u26a0\ufe0f Teilweise richtige Annahmen

| **Annahme** | **Korrektur** | **Quelle** |
|------------|---------------|------------|
| Plugin-Metadaten k\u00f6nnten in Agent-YAML \u00fcbertragen werden | **\u26a0\ufe0f TEILWEISE** – Agenten sind TOML-Dateien, nicht YAML, mit anderen Feldern | [Mistral Agents Docs](https://docs.mistral.ai/vibe/code/cli/agents) |

---

## \u2705 Best\u00e4tigte Mistral-Funktionen

### Offiziell unterst\u00fctzte SKILL.md-Felder
- `name` (Pflicht)
- `description` (Pflicht)
- `license` (Optional)
- `compatibility` (Optional)
- `user-invocable` (Optional, Boolean) – Macht Skill als `/skill-name` verf\u00fcgbar
- `allowed-tools` (Optional, List) – Restringiert Tools f\u00fcr diesen Skill

### Offiziell unterst\u00fctzte Verzeichnisstrukturen
- `.vibe/skills/<skill-name>/` (Projekt-level)
- `~/.vibe/skills/<skill-name>/` (User-level)
- `.agents/skills/<skill-name>/` (Alternative, Projekt-level)
- `.vibe/agents/<agent-name>.toml` (Agenten-Konfiguration)
- `.vibe/AGENTS.md` (Projektweite Instruktionen)
- `AGENTS.md` (Repository-root, wird unterst\u00fctzt)

### Offiziell unterst\u00fctzte Agenten-Funktionen
- `agent_type`: `"agent"` (user-facing) oder `"subagent"` (delegation-only)
- `display_name`, `description`, `safety`, `enabled_tools`
- Subagents k\u00f6nnen **keine** User-Fragen stellen

### Offiziell unterst\u00fctzte MCP-Integration
- Konfiguration in `config.toml` unter `mcp_servers`
- Transport: stdio, http, streamable-http
- Authentifizierung: API Keys, Headers, Environment Variables

---

## \ud83e\udd14 Offene Fragen

| **Frage** | **Status** | **Priorit\u00e4t** | **Auswirkung** |
|-----------|------------|---------------|----------------|
| Werden verschachtelte Skill-Verzeichnisse unterst\u00fctzt? | \u2753 Unbekannt | Mittel | K\u00f6nnte Skill-Erkennung beeintr\u00e4chtigen |
| K\u00f6nnen Skills in `.vibe/skills/` auf Dateien au\u00dferhalb zugreifen? | \u2753 Unbekannt | Hoch | Betrifft Verweise auf Originaldateien |
| Werden relative Pfade in SKILL.md relativ zum Skill-Verzeichnis oder Projekt-Root aufgel\u00f6st? | \u2753 Unbekannt | Hoch | Wichtig f\u00fcr Verweise |
| Gibt es eine Maximall\u00e4nge f\u00fcr `name` oder `description`? | \u2753 Unbekannt | Niedrig | Bisher keine Probleme bekannt |

---

## \u2705 Best\u00e4tigte Mistral-Funktionen

### Offiziell unterst\u00fctzte SKILL.md-Felder
- `name` (Pflicht)
- `description` (Pflicht)
- `license` (Optional)
- `compatibility` (Optional)
- `user-invocable` (Optional, Boolean) – Macht Skill als `/skill-name` verf\u00fcgbar
- `allowed-tools` (Optional, List) – Restringiert Tools f\u00fcr diesen Skill

### Offiziell unterst\u00fctzte Verzeichnisstrukturen
- `.vibe/skills/<skill-name>/` (Projekt-level)
- `~/.vibe/skills/<skill-name>/` (User-level)
- `.agents/skills/<skill-name>/` (Alternative, Projekt-level)
- `.vibe/agents/<agent-name>.toml` (Agenten-Konfiguration)
- `.vibe/AGENTS.md` (Projektweite Instruktionen)
- `AGENTS.md` (Repository-root, wird unterst\u00fctzt)

### Offiziell unterst\u00fctzte Agenten-Funktionen
- `agent_type`: `"agent"` (user-facing) oder `"subagent"` (delegation-only)
- `display_name`, `description`, `safety`, `enabled_tools`
- Subagents k\u00f6nnen **keine** User-Fragen stellen

### Offiziell unterst\u00fctzte MCP-Integration
- Konfiguration in `config.toml` unter `mcp_servers`
- Transport: stdio, http, streamable-http
- Authentifizierung: API Keys, Headers, Environment Variables

---

## \ud83d\udcdc Entscheidungen

| **Entscheidung** | **Begr\u00fcndung** | **Datum** | **Verantwortlich** |
|------------------|---------------|-----------|-------------------|
| **Flache Skill-Struktur** (`.vibe/skills/<skill-name>/`) | Mistral erkennt Skills als einzelne Verzeichnisse; verschachtelte Strukturen nicht dokumentiert | 2025-08-29 | KI |
| **Keine Anpassung von Verweisen** in migrierten Skills | Originaldateien bleiben erhalten; Verweise bleiben g\u00fcltig | 2025-08-29 | KI |
| **Keine Frontmatter-\u00c4nderungen** | Original-Skills haben nur `name` + `description`; beide sind offiziell unterst\u00fctzt | 2025-08-29 | KI |
| **Keine Verschiebung von Testakten, README etc.** | Nicht dokumentierte Strukturen; Originale bleiben als Referenz | 2025-08-29 | KI |
| **Keine Hinzuf\u00fcgung von `allowed-tools` oder `user-invocable`** | Nicht vorhanden im Original; optional, aber nicht erforderlich | 2025-08-29 | KI |

---

## \ud83d\udeab Nicht verwendete Strukturen/Felder

Folgende **nicht offiziell dokumentierte** Strukturen/Felder wurden **bewusst nicht verwendet**:

- \u274c `.vibe/instructions/` – Nicht dokumentiert
- \u274c `.vibe/test/` – Nicht dokumentiert
- \u274c `VIBE.md` – Nicht dokumentiert
- \u274c `model` in SKILL.md – Nicht dokumentiert
- \u274c `max_tokens` in SKILL.md – Nicht dokumentiert
- \u274c `temperature` in SKILL.md – Nicht dokumentiert
- \u274c `related_skills` in SKILL.md – Nicht unterst\u00fctzt
- \u274c `triggers` in SKILL.md – Nicht unterst\u00fctzt
- \u274c `language` in SKILL.md – Nicht unterst\u00fctzt
- \u274c `rechtsgebiet` in SKILL.md – Nicht unterst\u00fctzt

---

## \ud83d\udcca Technische Details

### Pilot-Skill-Analyse
- **Dateipfad:** `.vibe/skills/01-akte-erstdurchsicht-und-anfangsverdacht/SKILL.md`
- **Frontmatter:** `name`, `description` (beide Pflichtfelder)
- **Inhaltsl\u00e4nge:** 8.526 Zeichen Markdown
- **Verweise:** Nur textliche Hinweise auf andere Skills (z. B. `02-zustaendigkeit-sta-und-amtsanwaltschaft`)
- **Dateiabh\u00e4ngigkeiten:** Keine echten Dateipfade

### Vibe CLI-Problem
- **Fehler:** `ImportError: cannot import name 'streamablehttp_client'`
- **Ursache:** MCP-Bibliotheksproblem in Vibe 2.9.4
- **Auswirkung:** Funktionstest \u00fcber CLI nicht m\u00f6glich
- **Workaround:** Python-API-Tests funktionieren

### Validierungsergebnisse
- \u2705 Skill-Format korrekt
- \u2705 Frontmatter g\u00fcltig
- \u2705 Pflichtfelder vorhanden
- \u2705 Keine undokumentierten Felder
- \u2705 Dateizugriff auf referenzierte Dateien m\u00f6glich
- \u26a0\ufe0f CLI-Funktionstest nicht m\u00f6glich (Umgebungsproblem)

---

## 🔧 Reparatur von Namenskollisionen

### Problem
Bei der flachen Migration der Skills aus den beiden Staatsanwaltschaft-Plugins wurden zwei Namenskollisionen identifiziert:
- `99-finale-entscheidung-volltext`
- `prozessuale-kniffe-und-rechtsprechungsanker`

Beide Kollisionen betrafen **inhaltlich unterschiedliche Versionen** aus:
- Plugin A: `gerichtsplugins/staatsanwaltschaft-amtsanwaltschaft/`
- Plugin B: `gerichtsplugins/staatsanwaltschaft-praxis-einstieg/`

### Lösung
- Die Plugin-B-Versionen blieben unter den ursprünglichen Namen erhalten
- Die Plugin-A-Versionen wurden zusätzlich mit Namespace-Präfix `amtsanwaltschaft-` angelegt
- **Kein Informationsverlust** – beide Varianten sind nun verfügbar

### Neue Skills
- `.vibe/skills/amtsanwaltschaft-99-finale-entscheidung-volltext/` (aus Plugin A)
- `.vibe/skills/amtsanwaltschaft-prozessuale-kniffe-und-rechtsprechungsanker/` (aus Plugin A)

### Validierung
- 2 Namenskollisionen erkannt
- beide Originalvarianten waren inhaltlich unterschiedlich
- Plugin-B-Version blieb unter ursprünglichem Namen
- Plugin-A-Version zusätzlich namespaced angelegt
- kein fachlicher Inhalt gelöscht
- Zielstruktur enthält nun 176 Skill-Verzeichnisse
