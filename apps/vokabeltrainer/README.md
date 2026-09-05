# Vokabeltrainer — Offline-Version

Eine einzige HTML-Datei. Doppelklick genügt: kein Server, kein Build, keine
Netzverbindung, keine Konten. Alles läuft im Browser, gespeichert wird lokal.

Die App ist sprachunabhängig: ein **Kurs** ist eine Sprache (mit eigener
Ausgangs- und Zielsprache und eigener Abfragerichtung), darunter liegen
**Themen**, darunter die **Vokabeln**. Weitere Sprachen legst du selbst an.

Mitgeliefert sind zwei Kurse:

| Kurs | Umfang | Inhalt |
|---|---|---|
| Spanisch | 332 Vokabeln, 9 Themen | der bestehende Bestand |
| Englisch C1–C2 | 1000 Vokabeln, 25 Themen | Fach- und Abstraktwortschatz, Phrasal Verbs, Idiome, Kollokationen, Konnektoren, präzise Adjektive und Verben, falsche Freunde, formelles Register |

Jede englische Vokabel hat einen Beispielsatz; 98 % davon taugen als
Lückensatz. Verben stehen als `to …` — beim Eintippen zählt beides,
mit und ohne `to`.

**Der Englisch-Kurs für eine bestehende Installation:** `englisch-c1-c2.json`
unter **Daten → Datei wählen → Block prüfen → Zusammenführen** einlesen. Der
Startbestand greift nur beim allerersten Öffnen; wer schon einen Stand hat,
holt den Kurs über diesen Weg.

## Öffnen

`index.html` im Browser öffnen — per Doppelklick oder Rechtsklick → Öffnen mit.

Für ein eigenes Fenster mit Icon in Dock, Taskleiste oder Startmenü:
in Chrome oder Edge die Datei öffnen, dann ⋮ → *Speichern und teilen* (in
älteren Versionen *Weitere Tools*) → **Verknüpfung erstellen…**, Häkchen bei
*Als Fenster öffnen*. Das Ergebnis startet ohne Adressleiste und lässt sich
anheften. Ohne Browsermenü geht es auch klassisch: Windows Rechtsklick →
*Senden an* → *Desktop (Verknüpfung erstellen)*, macOS Rechtsklick →
*Alias erzeugen* bzw. die Datei in den rechten Bereich des Docks ziehen.

### Woran der Lernstand hängt

Am **Browserprofil**, nicht an der Datei. In Chromium geprüft:

| Situation | Stand vorhanden |
|---|---|
| dieselbe Datei erneut geöffnet | ja |
| Datei verschoben oder umbenannt | ja |
| Kopie der Datei in einem anderen Ordner | ja |
| anderer Browser oder anderes Profil | **nein** |
| über `http://localhost` statt `file://` geöffnet | **nein** |

Die Datei darfst du also verschieben und beim Aktualisieren überschreiben —
der Stand bleibt. Was ihn verliert: ein anderer Browser, ein zweites
Browserprofil, ein lokaler Webserver, der private Modus und das Löschen der
Browserdaten. Deshalb gilt trotzdem: **regelmäßig exportieren.**

Öffne die Datei immer mit demselben Browser. Startet sie einmal in einem
anderen, wirkt die App leer — die Daten sind nicht weg, sie liegen beim
anderen Browser. (Firefox und Safari wurden nicht geprüft; sie behandeln
`file://`-Speicher teils strenger.)

## Lernen

| Modus | Was passiert |
|---|---|
| Karteikarten | Abfrage mit vier Bewertungen; der Wiederholungsplan (SM-2) rechnet daraus die nächsten Intervalle |
| Übungen | 10 Aufgaben pro Runde, abwechselnd Auswahl und freies Eintippen; Tippfehler und fehlende Akzente werden als „fast richtig“ gewertet |
| Lückentext | Ein Lückensatz-Blatt aus den Beispielsätzen des Themas — komplett offline erzeugt, ohne Modellaufruf |

Tastatur: `Leertaste` Lösung zeigen · `1`–`4` bewerten bzw. Antwort wählen ·
`Enter` prüfen und weiter · `Esc` zurück.

Damit der Lückentext funktioniert, brauchen mindestens drei Vokabeln des Themas
einen Beispielsatz, der das Wort auch enthält. Gesucht wird die Form, die im
Satz steht — auch gebeugt („solicité“, „ascendieron“).

## Export und Import

Unter **Daten** liegt ein Textfeld, das in beide Richtungen arbeitet.

**Export** — „Block erzeugen“, dann „Kopieren“ oder „Als Datei sichern“.
Drei Umfänge stehen zur Wahl:

- *Vokabeln + Lernstand* — der vollständige Stand (rund 30 kB)
- *nur Vokabeln* — Kurse, Themen, Karten ohne Fortschritt
- *nur Lernstand* — nur die Intervalle, wenige hundert Zeichen; ideal, wenn
  beide Seiten dieselben Vokabeln haben und nur der Fortschritt wandern soll

Der vollständige Block ist mit beiden Kursen rund 140 kB groß. Für den
regelmäßigen Abgleich genügt meist *nur Lernstand* — ein paar hundert Zeichen.
„Als Datei sichern“ funktioniert lokal wie in der Claude-Version (dort fragt
die Umgebung vor dem Speichern nach).

**Import** — Block einfügen (oder „Datei wählen“), „Block prüfen“ zeigt an, was
drinsteckt, danach:

- **Zusammenführen** — nichts Lokales geht verloren. Neue Kurse, Themen und
  Vokabeln kommen dazu; bei jeder Karte gewinnt der weiter fortgeschrittene
  Lernstand (mehr Wiederholungen, sonst das spätere Fälligkeitsdatum).
  Gleiche Karten werden über ihre ID erkannt, sonst über identischen Wortlaut —
  doppelte Einträge entstehen also nicht.
- **Ersetzen** — der hiesige Stand wird verworfen und exakt durch den Block
  ersetzt. Zweistufig bestätigt.

## Beide Versionen synchron halten

Diese Datei läuft **auch in der Claude-App**: findet sie dort `window.storage`,
speichert sie darüber, sonst im `localStorage`. Damit sind die Claude-Version
und die Desktop-Version dieselbe App, und der Stand lässt sich mit demselben
Textblock in beide Richtungen schieben.

Als Artifact veröffentlicht (gleicher Stand, gleiche Oberfläche):
<https://claude.ai/code/artifact/a2da5ed2-7d7b-47ce-a0f0-52aaba458d9f>

Die Artifact-Fassung entsteht aus dieser Datei, indem `<!doctype>`, `<html>`,
`<head>` und `<body>` entfernt werden — `<title>`, `<style>` und der Rumpf
bleiben unverändert. Dort wird zusätzlich die `downloads`-Fähigkeit deklariert,
damit „Als Datei sichern“ auch im Artifact funktioniert.

1. In Version A: **Daten → Block erzeugen → Kopieren**
2. In Version B: **Daten → einfügen → Block prüfen → Zusammenführen**

Der Block ist reines JSON — lesbar, versionierbar, per Chat, Mail oder
Notizzettel transportierbar.

### Format

```json
{
  "app": "vokabeltrainer",
  "format": 1,
  "v": 3,
  "at": "2026-09-05T17:43:10.128Z",
  "deck": {
    "v": 3,
    "courses": [{
      "id": "c-es", "name": "Spanisch",
      "from": "Deutsch", "to": "Spanisch",
      "color": "#D9A227", "dir": "a-b",
      "themes": [{
        "id": "t0", "name": "Arbeit & Beruf", "color": "#B45309",
        "cards": [{ "id": "s0-0", "a": "die Frist", "b": "el plazo",
                    "ex": "Hay que entregarlo dentro del plazo." }]
      }]
    }]
  },
  "progress": {
    "dir": "a-b", "newPerDay": 15, "srsFromExercises": true,
    "day": "2026-09-05", "newDone": 3,
    "cards": { "s0-0": { "ef": 2.6, "iv": 30, "reps": 5, "lapses": 0, "due": 1789062142802 } }
  }
}
```

- `a` ist die Ausgangssprache, `b` die Zielsprache, `ex` der Beispielsatz.
- `dir`: `"a-b"`, `"b-a"` oder `"mix"` — pro Kurs, sonst gilt der Wert aus `progress`.
- `progress.cards` ist nach Karten-ID indiziert: `ef` Leichtigkeitsfaktor,
  `iv` Intervall in Tagen, `due` Fälligkeit als Zeitstempel in Millisekunden.
- `deck` und `progress` sind jeweils für sich importierbar.

Der Importer nimmt zusätzlich das **alte Format** der Spanisch-Version an
(`{"themes":[{"cards":[{"de":…,"es":…}]}]}` sowie deren Fortschrittsobjekt) und
wandelt es um. Liegen beim ersten Start noch die alten Speicherschlüssel
`spanisch-deck-v2` / `spanisch-srs-v2` im Browser, übernimmt die App sie von
selbst.

## Vokabeln pflegen

Thema öffnen → „Vokabeln verwalten“. Einzeln anlegen oder über „Mehrere
einfügen“ zeilenweise, getrennt durch `=`, Tab oder Semikolon:

```
die Frist = el plazo = Hay que entregarlo dentro del plazo.
die freie Stelle = la vacante
```

Das dritte Feld (Beispielsatz) ist optional, macht aber den Lückentext möglich.

## Icon

`icon.svg` — zwei Karteikarten auf marineblauem Grund, die vordere cremefarben
mit einem ockerfarbenen Gleichheitszeichen: das Trennzeichen, mit dem die App
auch Vokabeln einliest (`die Frist = el plazo`). Farben aus der Oberfläche
(`#0E2440`, `#F7F5EF`, `#D9A227`).

Das Icon steckt als Data-URI in `index.html` — als SVG für die Adressleiste,
als 64-px-PNG für Browser ohne SVG-Favicons und als randlose 180-px-Kachel für
`apple-touch-icon` (iOS legt seine eigene Maske an, deshalb ohne eigene Ecken).
Dazu `theme-color` und ein Kurzname für den Home-Bildschirm. `icon.svg` ist die
Quelle; die PNGs entstehen daraus, etwa per Browser-Screenshot.

## Technik

Eine Datei, kein Build, keine Abhängigkeiten, kein Netzverkehr. Getestet mit
Chromium über `file://`: Navigation, Karteikarten, Übungen, Lückentext,
Export/Import (Zusammenführen und Ersetzen), Migration aus der alten Version,
Löschpfade und Persistenz über einen Neustart.
