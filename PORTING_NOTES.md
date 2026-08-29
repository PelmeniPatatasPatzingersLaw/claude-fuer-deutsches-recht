# PORTING_NOTES.md – Technische Hinweise zur Portierung

---

## 🔍 Korrigierte Annahmen aus der ersten Analyse

### ❌ Falsche Annahmen (korrigiert)

| **Falsche Annahme** | **Korrektur** | **Quelle** |
|---------------------|---------------|------------|
| `allowed-tools` sei nicht unterstützt | **✅ WIRD UNTERSTÜTZT** – Offizielles SKILL.md-Feld | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| `user-invocable` sei nicht unterstützt | **✅ WIRD UNTERSTÜTZT** – Offizielles SKILL.md-Feld | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| Diese Felder müssten aus SKILL.md entfernt werden | **❌ FALSCH** – Beide Felder sind offiziell unterstützt | [Mistral Skills Docs](https://docs.mistral.ai/vibe/code/cli/skills) |
| `.vibe/instructions/` sei offizielle Struktur | **❌ NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `.vibe/test/` sei offizielle Struktur | **❌ NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `VIBE.md` sei Äquivalent zu `CLAUDE.md` | **❌ NICHT DOKUMENTIERT** – Nicht verwenden | Keine offizielle Dokumentation |
| `model`, `max_tokens`, `temperature` seien zulässig | **❌ NICHT DOKUMENTIERT** – Nicht verwenden | Nicht in Agent Skills Spec |
| `related_skills` sei unterstützt | **❌ NICHT DOKUMENTIERT** – Nicht verwenden | Nicht in Agent Skills Spec |

### ⚠️ Teilweise richtige Annahmen

| **Annahme** | **Korrektur** | **Quelle** |
|------------|---------------|------------|
| Plugin-Metadaten könnten in Agent-YAML übertragen werden | **⚠️ TEILWEISE** – Agenten sind TOML-Dateien, nicht YAML, mit anderen Feldern | [Mistral Agents Docs](https://docs.mistral.ai/vibe/code/cli/agents) |

---

## ✅ Bestätigte Mistral-Funktionen

### Offiziell unterstützte SKILL.md-Felder
- `name` (Pflicht)
- `description` (Pflicht)
- `license` (Optional)
- `compatibility` (Optional)
- `user-invocable` (Optional, Boolean) – Macht Skill als `/skill-name` verfügbar
- `allowed-tools` (Optional, List) – Restringiert Tools für diesen Skill

### Offiziell unterstützte Verzeichnisstrukturen
- `.vibe/skills/<skill-name>/` (Projekt-level)
- `~/.vibe/skills/<skill-name>/` (User-level)
- `.agents/skills/<skill-name>/` (Alternative, Projekt-level)
- `.vibe/agents/<agent-name>.toml` (Agenten-Konfiguration)
- `.vibe/AGENTS.md` (Projektweite Instruktionen)
- `AGENTS.md` (Repository-root, wird unterstützt)

### Offiziell unterstützte Agenten-Funktionen
- `agent_type`: `"agent"` (user-facing) oder `"subagent"` (delegation-only)
- `display_name`, `description`, `safety`, `enabled_tools`
- Subagents können **keine** User-Fragen stellen

### Offiziell unterstützte MCP-Integration
- Konfiguration in `config.toml` unter `mcp_servers`
- Transport: stdio, http, streamable-http
- Authentifizierung: API Keys, Headers, Environment Variables

---

## 🤔 Offene Fragen

| **Frage** | **Status** | **Priorität** | **Auswirkung** |
|-----------|------------|---------------|----------------|
| Werden verschachtelte Skill-Verzeichnisse unterstützt? | ❓ Unbekannt | Mittel | Könnte Skill-Erkennung beeinträchtigen |
| Können Skills in `.vibe/skills/` auf Dateien außerhalb zugreifen? | ❓ Unbekannt | Hoch | Betrifft Verweise auf Originaldateien |
| Werden relative Pfade in SKILL.md relativ zum Skill-Verzeichnis oder Projekt-Root aufgelöst? | ❓ Unbekannt | Hoch | Wichtig für Verweise |
| Gibt es eine Maximallänge für `name` oder `description`? | ❓ Unbekannt | Niedrig | Bisher keine Probleme bekannt |

---

## 📌 Entscheidungen

| **Entscheidung** | **Begründung** | **Datum** | **Verantwortlich** |
|------------------|---------------|-----------|-------------------|
| **Flache Skill-Struktur** (`.vibe/skills/<skill-name>/`) | Mistral erkennt Skills als einzelne Verzeichnisse; verschachtelte Strukturen nicht dokumentiert | 2025-08-29 | KI |
| **Keine Anpassung von Verweisen** in migrierten Skills | Originaldateien bleiben erhalten; Verweise bleiben gültig | 2025-08-29 | KI |
| **Keine Frontmatter-Änderungen** | Original-Skills haben nur `name` + `description`; beide sind offiziell unterstützt | 2025-08-29 | KI |
| **Keine Verschiebung von Testakten, README etc.** | Nicht dokumentierte Strukturen; Originale bleiben als Referenz | 2025-08-29 | KI |
| **Keine Hinzufügung von `allowed-tools` oder `user-invocable`** | Nicht vorhanden im Original; optional, aber nicht erforderlich | 2025-08-29 | KI |

---

## 🚀 Nicht verwendete Strukturen/Felder

Folgende **nicht offiziell dokumentierte** Strukturen/Felder wurden **bewusst nicht verwendet**:

- ❌ `.vibe/instructions/` – Nicht dokumentiert
- ❌ `.vibe/test/` – Nicht dokumentiert
- ❌ `VIBE.md` – Nicht dokumentiert
- ❌ `model` in SKILL.md – Nicht dokumentiert
- ❌ `max_tokens` in SKILL.md – Nicht dokumentiert
- ❌ `temperature` in SKILL.md – Nicht dokumentiert
- ❌ `related_skills` in SKILL.md – Nicht dokumentiert
- ❌ `triggers` in SKILL.md – Nicht unterstützt
- ❌ `language` in SKILL.md – Nicht unterstützt
- ❌ `rechtsgebiet` in SKILL.md – Nicht unterstützt

---

## 🔧 Technische Details

### Pilot-Skill-Analyse
- **Dateipfad:** `.vibe/skills/01-akte-erstdurchsicht-und-anfangsverdacht/SKILL.md`
- **Frontmatter:** `name`, `description` (beide Pflichtfelder)
- **Inhaltslänge:** 8.526 Zeichen Markdown
- **Verweise:** Nur textliche Hinweise auf andere Skills (z. B. \`02-zustaendigkeit-sta-und-amtsanwaltschaft\`)
- **Dateiabhängigkeiten:** Keine echten Dateipfade

### Vibe CLI-Problem
- **Fehler:** `ImportError: cannot import name 'streamablehttp_client'`
- **Ursache:** MCP-Bibliotheksproblem in Vibe 2.9.4
- **Auswirkung:** Funktionstest über CLI nicht möglich
- **Workaround:** Python-API-Tests funktionieren

### Validierungsergebnisse
- ✅ Skill-Format korrekt
- ✅ Frontmatter gültig
- ✅ Pflichtfelder vorhanden
- ✅ Keine undokumentierten Felder
- ✅ Dateizugriff auf referenzierte Dateien möglich
- ⚠️ CLI-Funktionstest nicht möglich (Umgebungsproblem)
