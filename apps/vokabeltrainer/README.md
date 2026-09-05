# Vokabeltrainer — Offline-Version

Eine einzige HTML-Datei. Doppelklick genügt: kein Server, kein Build, keine
Netzverbindung, keine Konten. Alles läuft im Browser, gespeichert wird lokal.

Die App ist sprachunabhängig: ein **Kurs** ist eine Sprache (mit eigener
Ausgangs- und Zielsprache und eigener Abfragerichtung), darunter liegen
**Themen**, darunter die **Vokabeln**. Beim ersten Start ist der Spanisch-Kurs
mit 332 Vokabeln in neun Themen vorhanden; weitere Sprachen legst du selbst an.

## Öffnen

`index.html` im Browser öffnen — per Doppelklick oder Rechtsklick → Öffnen mit.
Für den Dock/Startmenü-Zugriff die Datei als Lesezeichen ablegen oder in
Chrome/Edge über „Als App installieren“ ein eigenes Fenster erzeugen.

Der Stand liegt im `localStorage` **dieses** Browsers und dieses Dateipfads.
Verschiebst du die Datei in einen anderen Ordner, sieht der Browser sie unter
Umständen als andere Seite und der Stand ist zunächst leer — dann den Export
von vorher importieren. Deshalb: **regelmäßig exportieren.**

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

## Technik

Eine Datei, kein Build, keine Abhängigkeiten, kein Netzverkehr. Getestet mit
Chromium über `file://`: Navigation, Karteikarten, Übungen, Lückentext,
Export/Import (Zusammenführen und Ersetzen), Migration aus der alten Version,
Löschpfade und Persistenz über einen Neustart.
