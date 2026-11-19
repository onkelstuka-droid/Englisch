# Vokabelheft Englisch – Projektübergabe für Claude

App-Link: https://onkelstuka-droid.github.io/Englisch/
Repository: github.com/onkelstuka-droid/Englisch (Branch `main`, GitHub Pages aus `/ (root)`)
Nutzer: **Hans-Jürgen Schlichting** (spricht Deutsch, Android-Handy Pixel, kein Programmierer). Auch Idee des Schichtplaners (github.com/onkelstuka-droid/Schichtplan-).
Umsetzung: Claude. Immer auf Deutsch antworten, einfach erklären, Schritte für das Handy beschreiben.
Wünsche: Englisch leicht lernen, **keine Bilder** in den Übungen, Übungen, gespeicherte Erfolge und Fortschritt, Sprachausgabe.

## Aufbau
- `src/vokabelheft.html` – **die gesamte App in einer Datei** (HTML, CSS, JS). Nur hier ändern.
- `build.py` – baut daraus `index.html` (Head, Manifest-Link, Service-Worker-Registrierung), `sw.js` (Offline-Cache `englisch-app-<hash>`), `manifest.webmanifest`, `404.html`; erzeugt `icons/*.png` nur, wenn sie fehlen (Schrift `fonts/SPSans-Bold.ttf`, SIL OFL).
- Veröffentlichen: `python3 build.py`, committen, `git push origin main`. GitHub Pages aktualisiert in 1–2 Min.
- Gleiche Herkunft (onkelstuka-droid.github.io) wie der Schichtplaner: localStorage und Cache-Speicher werden geteilt. Deshalb eigene Präfixe: Schlüssel `vokabelheft_en_v1`, Caches `englisch-app-*` (der SW löscht nur diese). Schichtplaner nutzt `sp_*` und `schichtplan-*`.
- Früher lag die App unter `Schichtplan-/englisch/`; dort ist jetzt nur noch eine Weiterleitung hierher und ein Service Worker, der sich selbst entfernt (löscht nur Caches `vokabelheft-*`). Der Fortschritt bleibt erhalten (gleicher localStorage).
- Eine Version läuft zusätzlich als Claude-Artifact (claude.ai/artifact/NBC18Ru7C1xvM7S2HU9BuV, speichert dort im Claude-Konto über die `db`-Fähigkeit). Weiterentwicklung hier.

## Inhalt
- `RAW`: 17 Themen (Begrüßung, Zahlen, Wochentage & Monate, Zeit, Familie, Essen, Verben, Arbeit & Schicht, Haus, Einkaufen, Eigenschaften, Farben, Kleidung, Körper & Gesundheit, Reisen, Wetter, Freizeit), je Zeile `englisch|deutsch`, Alternativen mit „ / “. Zweiter Block je Thema = Sätze.
- Schlüssel: `w:<thema>:<englisch>` bzw. `s:…`. Englischen Text ändern = Wort gilt als neu.
- `GRAM_RAW` (`kategorie|Satz mit ___|richtig|falsch1,falsch2|deutsch`) + `GCAT` (Name, Regel, die bei Fehlern gezeigt wird).

## Übungen und Logik
- Arten: mcEN (Englisch → Deutsch wählen), mcDE, listen (hören → Bedeutung), type (tippen, Tippfehler-Toleranz `lev`, Kurzformen `CONTRACT`), build/buildL (Satz aus Kärtchen, mit/ohne Ton), gram, speak (Spracherkennung), card (Karteikarte, selbst bewerten).
- Sitzungen (`buildSession`): daily („Tägliche Runde“: fällige Wörter, neue aus dem aktuellen Thema + Wiederholung, 2 Sätze, 2 Grammatik), topic, listen, write, build, gram (auch je Kategorie), speak, mistakes, cards, redo.
- Wiederholung (`grade`): `srs[key] = [box 0–6, fällig am Tag, gesehen, falsch]`, Abstände `[0,1,2,4,8,16,35]` Tage, „sitzt“ ab Box 3. Falsch → Box 0/1 und die Aufgabe kommt am Ende der Runde nochmal (ohne neue Wertung).
- XP: 10 je richtige Antwort, 15 für Schreiben/Sprechen/Hör-Satzbau, 5 je Karteikarte. Tagesziel (30/50/100/150), Stufen (`levelInfo`), Tage in Folge, Note 1–5 am Ende einer Runde, 20 Stempel (`ACH`, z. B. „Nachtschicht“ nach 22 Uhr, „Frühschicht“ vor 6 Uhr).
- Fortschritt: localStorage `vokabelheft_en_v1` (xp, days, srs, gram, ach, c = Zähler, set = Einstellungen, lastBak). Sicherung als JSON-Datei (Einstellungen → Sicherung speichern/laden).
- Vorlesen: `say()` – Handy-Stimme (speechSynthesis, britisch/amerikanisch, Tempo), in Claude zusätzlich Claude-Stimme (`tts`). Sprechen: `webDict()` (Chrome-Spracherkennung) bzw. in Claude die `dictation`-Fähigkeit.
- Design: Schulheft (blaue Tinte, roter Rand, gelber Textmarker), Schriften Bricolage Grotesque / Atkinson Hyperlegible / Caveat, hell und dunkel.

## Arbeitsweise
- Änderungen testen (Playwright/Chromium headless ist vorinstalliert, über `python3 -m http.server` laden), dann bauen und pushen.
- Neue Wörter/Themen einfach in `RAW` ergänzen.
