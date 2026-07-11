# malcolmelmiger.ch — Homepage

Statische One-Pager-Website. **Kein Jekyll, keine Gems, kein Bundler, kein npm, kein Build-Schritt.** Vercel liefert die Dateien direkt aus – damit kann beim Deployment nichts mehr schiefgehen.

## Struktur

```
index.html    → die gesamte Website (HTML + CSS + JS in einer Datei)
404.html      → Fehlerseite (wird von Vercel automatisch verwendet)
favicon.svg   → Browser-Icon (M-Monogramm, bei Bedarf ersetzen)
vercel.json   → saubere URLs + Sicherheits-Header
```

## Schritt 0 — Zwei Dateien noch hinzufügen

Die Seite verweist auf zwei Dateien, die du selbst ins Repo legen musst (direkt neben `index.html`):

- `portrait.jpg` — dein Porträtfoto
- `Lebenslauf.pdf` — dein CV (Achtung: Gross-/Kleinschreibung muss exakt stimmen)

Solange sie fehlen, funktioniert die Seite trotzdem – nur Bild und CV-Link laufen ins Leere.

## Schritt 1 — Auf GitHub bringen

Am saubersten ersetzt du den Inhalt deines bestehenden Repos komplett:

```bash
git clone https://github.com/frameoperation/Homepage.git
cd Homepage
git checkout homepage        # dein bisheriger Branch

# Alles Alte löschen (ausser dem versteckten .git-Ordner!),
# dann die Dateien aus diesem Paket hineinkopieren.

git add -A
git commit -m "Jekyll durch statische Seite ersetzt"
git push
```

Alternativ ein frisches Repo auf github.com anlegen ("New repository", ohne README), dann:

```bash
cd malcolmelmiger-homepage
git init
git add -A
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/frameoperation/NEUER-NAME.git
git push -u origin main
```

## Schritt 2 — Vercel konfigurieren (wichtig!)

Dein bisheriges Vercel-Projekt hat noch `bundle install` als Install-Befehl hinterlegt – das muss weg.

1. Vercel-Dashboard → dein Projekt → **Settings → Build and Deployment**
2. **Framework Preset:** `Other`
3. **Build Command:** leer lassen (Override ausschalten)
4. **Output Directory:** leer lassen (Override ausschalten)
5. **Install Command:** leer lassen – den alten `bundle install`-Eintrag löschen (Override ausschalten)
6. **Root Directory:** leer bzw. `./`
7. Speichern und unter **Deployments → ⋯ → Redeploy** neu deployen

Noch einfacher: das alte Vercel-Projekt löschen und das Repo als neues Projekt importieren ("Add New → Project"). Vercel erkennt dann automatisch eine statische Seite ohne Build.

## Schritt 3 — Domain von Hostpoint verbinden

**In Vercel:** Projekt → Settings → Domains → `malcolmelmiger.ch` eintragen. Vercel schlägt vor, auch `www.malcolmelmiger.ch` hinzuzufügen mit Weiterleitung – annehmen. (Die Seite nutzt `www.` als kanonische URL, also idealerweise die Weiterleitung von Apex → www wählen.) Vercel zeigt dir dann die genauen DNS-Werte an.

**Bei Hostpoint:** Im Hostpoint Control Panel (admin.hostpoint.ch) → **Domains** → `malcolmelmiger.ch` → **DNS-Einstellungen / Zone bearbeiten**:

| Typ   | Name / Host | Wert                   |
|-------|-------------|------------------------|
| A     | `@` (leer)  | `76.76.21.21`          |
| CNAME | `www`       | `cname.vercel-dns.com` |

Wichtige Hinweise:

- **Massgeblich sind die Werte, die dir das Vercel-Dashboard anzeigt.** Vercel vergibt teils projektspezifische CNAME-Werte (z. B. `xxxx.vercel-dns-017.com`) – dann exakt diesen Wert übernehmen, inklusive allfälligem Punkt am Ende.
- Bestehende A- oder AAAA-Einträge für `@` und `www`, die noch auf Hostpoint-Server zeigen, entfernen bzw. ersetzen.
- **MX- und Mail-Einträge nicht anfassen** – deine E-Mail läuft dann weiterhin über Hostpoint.
- TTL kurz setzen (z. B. 300), damit die Änderung schnell greift.

Danach in Vercel unter Settings → Domains auf **Refresh** klicken. Die Umstellung dauert meist wenige Minuten bis ein paar Stunden (DNS-Propagation). Das SSL-Zertifikat erstellt Vercel automatisch.

## Fehlersuche

- **"Invalid Configuration" in Vercel:** DNS noch nicht propagiert – warten und mit https://www.whatsmydns.net prüfen.
- **Alte Seite wird angezeigt:** Browser-Cache leeren, TTL des alten Eintrags abwarten.
- **Porträt oder CV wird nicht angezeigt:** Prüfen, ob `portrait.jpg` und `Lebenslauf.pdf` im Repo-Root liegen und exakt so heissen.
- **E-Mail funktioniert nicht mehr:** MX-Einträge bei Hostpoint prüfen – sie dürfen durch die Umstellung nicht verändert worden sein.
