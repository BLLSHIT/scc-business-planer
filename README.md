# Padel Business Plan Matrix

Interaktiver Business-Plan-Generator für Padel-Anlagen — von der Idee bis zum Investorengespräch. Mehrjahres-Forecasts, Standort-Scoring, DSCR, Cashflow-Plan, Excel-/PDF-Export.

**Live-Demo (nach Deployment):** `https://<dein-github-user>.github.io/padel-businessplan/`

---

## Setup in 3 Schritten

### 1. Supabase-Projekt anlegen

1. Auf [supabase.com](https://supabase.com) ein neues Projekt erstellen (Region: **EU** für DSGVO).
2. Im **SQL-Editor** den Inhalt von `supabase/schema.sql` einfügen → **Run**.
3. **Project Settings → API** → notiere `Project URL` und `anon public` Key.
4. **Authentication → Providers → Email** aktivieren (Magic Link reicht).
5. **Authentication → Settings → User Signups** → **Disable signups** aktivieren.
   ⚠️ Wichtig — sonst kann sich jeder mit beliebiger E-Mail registrieren.
6. **Authentication → Users → Add user** → für jeden, der Zugriff bekommen soll, manuell anlegen.

### 2. URL und Key in `index.html` eintragen

Öffne `index.html` und finde oben (ca. Zeile 19–20) den Block:

```js
const SUPABASE_URL      = "PLATZHALTER_URL";
const SUPABASE_ANON_KEY = "PLATZHALTER_KEY";
```

Ersetze die beiden Platzhalter mit deinen Werten:

```js
const SUPABASE_URL      = "https://xyz123.supabase.co";
const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6...";
```

Datei speichern. **Fertig.**

> Solange `SUPABASE_URL` nicht mit `https://` beginnt oder der Key kürzer als 40 Zeichen ist, läuft die App im **Demo-Modus** (LocalStorage only) ohne Login-Box.

### 3. Zu GitHub pushen + Pages aktivieren

```bash
cd "/Pfad/zu/diesem/Ordner"
git init
git add .
git commit -m "Initial: Padel Business Plan Matrix"
git branch -M main
git remote add origin git@github.com:<dein-user>/padel-businessplan.git
git push -u origin main
```

Im Repo: **Settings → Pages → Branch `main`, Folder `/ (root)` → Save**.

Nach 1–2 Minuten ist die App live unter `https://<dein-user>.github.io/padel-businessplan/`.

---

## Zugriffsschutz

Die App nutzt einen **Auth-Gate**: ohne Login wird der gesamte Generator verborgen, der User sieht nur einen Login-Screen.

Das wird durch drei Schichten gesichert:

| Schicht | Was es schützt | Wie aktivieren |
|---|---|---|
| **Auth-Gate (Frontend)** | Versteckt die UI vor nicht eingeloggten Usern | `REQUIRE_LOGIN = true` in `index.html` (default) |
| **Sign-up-Sperre** | Verhindert, dass sich neue User selbst registrieren | Supabase → Authentication → Settings → Disable signups |
| **Allowlist-Tabelle (optional)** | Erlaubt nur explizit freigegebene E-Mails | `allowed_emails`-Tabelle in `schema.sql` + strikte RLS-Policies |

**Wichtig:** Frontend-Auth-Gate allein reicht nicht — wer den Quellcode liest, kann das `scc-locked`-CSS umgehen. Erst die Sign-up-Sperre (oder Allowlist) auf Supabase-Seite macht das Tool wirklich zugangsbeschränkt, weil ohne gültigen User-Account auch keine Daten gelesen/geschrieben werden können (RLS blockt jeden anonymen Zugriff).

### REQUIRE_LOGIN umstellen

In `index.html`, Zeile ~22:

```js
const REQUIRE_LOGIN = true;   // Auth-Gate aktiv (App-UI verborgen ohne Login)
const REQUIRE_LOGIN = false;  // App ohne Login nutzbar, Cloud-Speicher nur nach Login
```

## Was passiert ohne Supabase-Konfig?

Wenn `SUPABASE_URL` und `SUPABASE_ANON_KEY` Platzhalter bleiben, läuft die App im **Demo-Modus**:
- Eingaben werden lokal im Browser gespeichert (Autosave)
- JSON-Export/Import funktioniert
- Keine Login-Box oben rechts, kein Auth-Gate
- Keine Cloud-Speicherung, keine Multi-Plan-Verwaltung

Das ist ein gültiger Setup, falls du den Generator allein nutzen und nicht öffentlich deployen willst.

---

## Mit Supabase aktiv

Sobald du einen Account in der App anlegst (Magic Link via Email), erscheinen oben rechts:

- **🔑 Login** → Magic Link via Email anfordern
- **💾 Speichern** → aktuellen Plan in Supabase ablegen (mit Namen)
- **☁ Pläne** → Liste aller eigenen Pläne, Laden / Löschen

Jeder Save legt zusätzlich eine Versionskopie in `business_plan_versions` an — du kannst nichts versehentlich überschreiben.

---

## Sicherheit & Datenschutz

- Alle Pläne sind per **Row-Level-Security** an die User-ID gebunden
- Niemand kann fremde Pläne lesen oder schreiben — auch nicht mit dem Anon-Key
- Der `anon public` Key darf öffentlich im Quellcode stehen (das ist sein Designziel) — er gibt nur Zugriff auf das, was die RLS-Policies erlauben
- Keine sensiblen Daten (Kreditkarten, IBAN) im Datenmodell
- DSGVO-konform bei Hosting in EU-Region

---

## Repo-Struktur

```
.
├── index.html              ← der Generator (alles inline, inkl. Supabase-Integration)
├── supabase/
│   └── schema.sql          ← einmalig in Supabase ausführen
├── README.md
├── LICENSE
└── .gitignore
```

Optionale Backup-Dateien (für modulare Setups):
- `config.example.js` — Vorlage falls du die Config in eine eigene Datei auslagern willst
- `supabase-integration.js` — der Integrationscode als separate Datei
- `integration-howto.md` — alternative Anleitung mit getrennten Dateien

Diese Dateien werden vom aktuellen Setup **nicht benötigt** — `index.html` ist self-contained.

---

## Features

- 8-Schritt-Wizard (Projekt → Standort → Courts → Auslastung → Umsätze → Kosten → Finanzierung → Generieren)
- Standort-Scoring (Demografie, Konkurrenz, Verkehr, Parkplätze)
- Korrekte G+V mit Zins/Tilgung-Trennung, Jahressteuern, Vermieter-Beteiligung
- Cashflow-Plan (vereinfacht direkt)
- DSCR pro Jahr, Break-Even-Indikator
- Best/Worst-Szenarien (volumen-elastisch)
- Investoren-Gewinnverteilung mit ROI p.a.
- Charts: Mehrjahres-Linie, Investitionsverteilung, Finanzierungsmix, Umsatzstruktur
- Excel-Export (9 Sheets), PDF-Export, PNG-Export
- LocalStorage Autosave + JSON Export/Import + Reset
- MwSt-Toggle Netto/Brutto
- Optional: Supabase Cloud-Speicher mit Multi-Plan, Versionierung, Magic-Link-Login

---

## Lizenz

MIT — siehe `LICENSE`. Du darfst die Anwendung kommerziell einsetzen, anpassen, weiterverteilen.
