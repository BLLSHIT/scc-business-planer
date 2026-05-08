# Padel Business Plan Matrix

Interaktiver Business-Plan-Generator für Padel-Anlagen — von der Idee bis zum Investorengespräch. Mehrjahres-Forecasts, Standort-Scoring, DSCR, Cashflow-Plan, Excel-/PDF-Export.

**Live-Demo (nach Deployment):** `https://<dein-github-user>.github.io/padel-businessplan/`

---

## Was ist drin

- **`index.html`** — der komplette Generator (Single-File, kein Build-Step nötig)
- **`supabase/schema.sql`** — Datenbank-Schema mit Row-Level-Security
- **`supabase-integration.js`** — Login, Speichern, Laden, Liste, Löschen
- **`config.example.js`** — Vorlage für deine Supabase-URL + Anon-Key
- **`integration-howto.md`** — wie du die Supabase-Anbindung in `index.html` aktivierst
- **`.gitignore`** — schützt `config.js` und Lokales

Ohne Supabase funktioniert die App auch — sie speichert dann automatisch in LocalStorage.

---

## Setup-Anleitung

### 1. GitHub-Repo anlegen

```bash
cd "/Pfad/zu/diesem/Ordner"
git init
git add .
git commit -m "Initial: Padel Business Plan Matrix"
git branch -M main
git remote add origin git@github.com:<dein-user>/padel-businessplan.git
git push -u origin main
```

Im Repo-Settings → **Pages** → Branch `main`, Folder `/ (root)`, **Save**. Nach 1–2 Minuten ist die App unter `https://<dein-user>.github.io/padel-businessplan/` erreichbar.

### 2. Supabase-Projekt anlegen

1. Auf [supabase.com](https://supabase.com) ein neues Projekt erstellen (Region: EU für DSGVO).
2. SQL-Editor öffnen → Inhalt von `supabase/schema.sql` einfügen → **Run**.
3. **Project Settings → API** → notiere `Project URL` und `anon public` Key.

### 3. Konfiguration

```bash
cp config.example.js config.js
```

`config.js` öffnen und einfügen:

```js
window.SUPABASE_CONFIG = {
  url: 'https://<dein-projekt>.supabase.co',
  anonKey: 'eyJhbGciOi...'
};
```

> ⚠️ `config.js` ist in `.gitignore` — wird nicht zu GitHub gepusht. Den Anon-Key kannst du theoretisch öffentlich machen (er ist nur durch RLS geschützt), trotzdem behandle ihn wie ein Secret.

### 4. Auth aktivieren

In Supabase → **Authentication → Providers** → **Email** aktivieren (Magic Link reicht für den Anfang). Optional: Google OAuth, GitHub OAuth.

### 5. Integration einbauen

Siehe `integration-howto.md` — drei Skript-Tags und ein Login-Button-Block in `index.html`.

---

## Features

### Bereits in `index.html` enthalten

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

### Über Supabase (optional)

- Cloud-Speicherung mehrerer Pläne (z.B. mehrere Standortvarianten)
- Multi-User: Team-Mitglieder können auf eigene Pläne zugreifen
- Versionierung (jeder Save = neue Version)
- Plan-Sharing per Link (Read-Only-Modus)

---

## Sicherheit & Datenschutz

- Alle Pläne sind per **Row-Level-Security** an die User-ID gebunden
- Niemand kann fremde Pläne lesen oder schreiben — auch nicht mit dem Anon-Key
- Keine sensiblen Daten (Kreditkarten, IBAN, etc.) im Datenmodell
- DSGVO-konform bei Hosting in EU-Region

---

## Lizenz

MIT — siehe `LICENSE`. Du darfst die Anwendung kommerziell einsetzen, anpassen, weiterverteilen.
