# loop break · Meldeader-Kapazität

Web- und Appanwendung zur Berechnung von Leitungslängen überwachter Fernwärmeleitungen
anhand von Kapazitätsmessungen an der Meldeader.

---

## Grundprinzip

Die App berechnet die Leitungslänge aus dem gemessenen Kapazitätswert (pF)
mithilfe der **exzentrischen Zwei-Zylinder-Formel**:

```
C/L = 2π · ε₀ · εᵣ / arcosh((R² + r² − e²) / (2·R·r))
```

- **R** = Außenradius Stahlmediumrohr (mm)
- **r** = Radius Meldeader (mm)
- **e** = Exzentrizität = R + δ + r
- **δ** = Abstand Drahtmitte → Rohrwand (mm)
- **εᵣ** = Dielektrizitätszahl PUR-Schaum (Standard 1,20)

Die Wandstärke des Mediumrohrs geht **nicht** in die Formel ein –
das elektrische Feld wirkt zwischen Stahl-Außenfläche und Draht.

---

## Empfohlene Messfrequenz

**1 kHz** – gilt für alle Leitungslängen im typischen Arbeitsbereich (30–500 m).
Begründung: ausreichend weit von der 50-Hz-Netzfrequenz entfernt,
Impedanz im optimalen Arbeitsbereich des Messgeräts,
Induktivitätseffekte bei diesen Leitungslängen vernachlässigbar.

---

## Vorgehensweise

### Schritt 1 · Mediumrohr bestimmen

Zwei Wege stehen zur Auswahl:

- **DN-Typ → Stahlrohr**: Rohrtyp aus Bestandsunterlagen bekannt →
  DN-Nummer wählen, Stahlaußendurchmesser wird automatisch gesetzt.
- **KMR-Außen-⌀**: Nur das Kunststoff-Premantrohr messbar →
  KMR-Durchmesser wählen. Die zugehörigen DN-Typen und Dämmstärken
  werden angezeigt.

### Schritt 2 · Meldeader und Drahttyp wählen

**Nordic (Kupferadern)**

| Drahttyp | Beschreibung |
|----------|-------------|
| Cu blank | Blanke Kupferader, 1,5 mm², ø 1,382 mm. Direkter Kontakt mit dem PUR-Schaum. Meistverbreitete Ausführung im nordischen System. Verzinnte Adern gelten als gleichwertig. |
| Cu/Sn verzinnt | Verzinnte Kupferader, 1,5 mm², ø 1,382 mm. Identische Geometrie zur blanken Ader; Verzinnung bietet leicht erhöhten Korrosionsschutz. Nicht mit blanken Adern mischen. |

Das nordische System nutzt zwei sich nicht berührende Kupferadern als Überwachungsschleife.
Ortung erfolgt primär über das Impulslaufzeit-Messverfahren (TDR).

**Brandes (NiCr-System)**

| Drahttyp | Beschreibung |
|----------|-------------|
| Fühlerader NiCr80/20 | Widerstandsdraht aus 80 % Nickel / 20 % Chrom, 0,189 mm², ø 0,490 mm. Perforierte Teflonisolierung lässt Feuchtigkeit an den Draht. Der hohe spezifische Widerstand (ρ ≈ 1,10 Ω·mm²/m) ermöglicht präzise Widerstandsortung (±0,2 % der Messstrecke). |
| Rückführader Cu | Vollisolierte Kupferader, 0,490 mm², ø 0,790 mm. Bildet gemeinsam mit der Fühlerader die Überwachungsschleife. Verbindung an jedem Rohrende. |

Das Brandes-System arbeitet nach dem Widerstands-Referenz-Messverfahren.
Vorteil: einheitliche Verdrahtung durch alle Hersteller, sehr genaue Ortung (±0,2 % der Messstrecke).

**KA-Tab – Ausführungskabel**

Dient zur Berechnung des Kapazitäts-Offsets für das Ausführungskabel
(8-adrig, Cu, DIN 47001). Aderquerschnitt (0,5 oder 1,0 oder 1,5 mm²), Kabellänge
und Aderposition (Benachbart / Übernächst / Gegenüber) eingeben →
errechneter Offset wird mit **„KA-Wert in Merk2"** direkt in Merk2 übertragen.

Die Berechnung verwendet die **Zweidrahtleiter-Formel** (parallele Zylinder):

```
C/L = π · ε₀ · εᵣ / arcosh(D / d)
```

- **d** = Aderdurchmesser, berechnet aus Querschnitt: d = √(4·A / π)
  - 0,5 mm² → d = 0,8 mm
  - 1,0 mm² → d = 1,128 mm
  - 1,5 mm² → d = 1,382 mm
- **D** = Abstand der Adermittelpunkte (mm)
  - Benachbart: D = 2,9 mm
  - Übernächst:  D = 5,4 mm
  - Gegenüber:   D = 7,5 mm
- **εᵣ** = 3,5 (PVC-Isolierung)

Im Gegensatz zur exzentrischen Zylinderformel der Hauptberechnung
(Draht exzentrisch im Rohr) beschreibt diese Formel zwei **parallel
nebeneinander** liegende Leiter. Da die genaue Aderposition im Kabel
nicht reproduzierbar bekannt ist, streut der Offset zwischen den drei
Varianten. Den gemessenen Istwert am Kabel ohne angeschlossene Leitung
direkt als Eichwert zu verwenden bleibt daher die zuverlässigste Methode.

### Schritt 3 · Dämmstärke und Abstand δ wählen

Nach Rohrauswahl erscheinen vier Karten mit den möglichen Dämmstärken
(Premantrohr-⌀ und δ-Wert). **Dämmstärke 2** ist vorausgewählt (Standard).
Der Slider erlaubt freie Korrektur in 0,5-mm-Schritten; die Kartenauswahl
bleibt dabei unberührt.

### Schritt 4 · Messwerte eingeben

| Feld | Inhalt | Stellen |
|------|--------|---------|
| Kapazität gemessen | Messwert am Gerät (pF) | max. 5 |
| Eichwert | Gerät + offene Messkabel (pF) | max. 3 |
| Merk1 | Gespeicherter Abgangs-Offset inkl. Eichwert (pF) | max. 5 |
| Merk2 | Gespeicherter Abgangs-Offset exkl. Eichwert (pF) | max. 4 |

**Eichwert**: Messung bei offenem Kabel ohne angeschlossene Leitung.

### Schritt 5 · Länge berechnen

Schaltfläche **„Länge berechnen ↗"** auslösen.

Das Ergebnis erscheint im gelben Feld:
- **Hauptwert**: berechnete Gesamtlänge
- **Abgang 1**: eingefrorene Länge aus Merk1 (zum Zeitpunkt von „Merk1")
- **Abgang 2**: eingefrorene Länge aus Merk2 oder KA-Kabellänge

Die Hauptberechnung zieht **Merk1 und Merk2 gleichzeitig** ab,
sofern beide Felder befüllt sind.

---

## Merkfelder – Workflow Leitungsabschnitte

Typischer Anwendungsfall: Messung über einen Hausabgang in die Hauptleitung.

1. **Abgangsparameter einstellen** (Rohrtyp, Draht, δ des Abgangs)
2. C-Wert so anpassen, bis die bekannte Abgangslänge angezeigt wird
3. **„Merk1"** drücken → Wert wird eingefroren (inkl. Eichwert)
4. Rohrtyp und Draht auf Hauptleitung umstellen
5. Gesamtmessung eingeben → **„Länge berechnen ↗"**
6. Ergebnis = reine Distanz vom T-Stück zur Schaltstelle

---

## Einstellungen

Über das ⚙-Symbol oben rechts erreichbar:

| Einstellung | Beschreibung |
|-------------|-------------|
| εᵣ | Dielektrizitätszahl PUR-Schaum (1,1 trocken bis 2,5 stark feucht) |
| Drahtdurchmesser | Messschieber-Istwert für alle Drahttypen |
| Standard-Eichwert | Wird beim Öffnen automatisch ins Eichwert-Feld übertragen |
| Gerätefaktor K | Kalibrierfaktor Messgerät (Standard 1,000) |
| Feldfaktor | Empirischer Korrekturfaktor (Standard 1,000) |
| Ausgabe | Meter oder Kilometer |

Alle Einstellungen werden lokal im Browser gespeichert (localStorage)
und bleiben nach dem Schließen erhalten.

---

## Hinweise zur Messung

- **Kapazitätsmessung nur bei Schleifenunterbruch** sinnvoll.
  Bei Feuchtigkeitsbrücke zwischen Ader und Rohr ist das Verfahren
  unzuverlässig (Parallelleitwert verfälscht den Messwert).
- **Messfrequenz 1 kHz** am Gerät einstellen (nicht 100 Hz, nicht 10 kHz).
- **εᵣ** bei trockenem, intaktem Schaum: 1,1–1,3.
  Bei Verdacht auf Alterung: 1,35–1,5 verwenden.
- Der **Eichwert** sollte mit angeschlossenem, aber nicht mit der
  Leitung verbundenem Kabel gemessen werden.

---

## Kompatibilität

Die App läuft als einzelne HTML-Datei im Browser –
kein Server, keine Installation, keine Internetverbindung nötig.
Getestet auf Chrome/Android und Desktop-Browsern.

Für den Einsatz über GitHub Pages: Datei als `index.html` im Repository
ablegen und Pages unter *Settings → Pages* aktivieren.

---

## Glossar

| Kürzel | Bedeutung |
|--------|-----------|
| FW | Fernwärme |
| VL / RL | Vorlauf / Rücklauf |
| MK | Messkreis |
| SÖ | Schadenöffnung |
| KMR | Kunststoffmantelrohr (Premantrohr) |
| KA | Kabelausführung (Ausführungskabel) |
| DS | Dämmstärke |
| δ | Abstand Drahtmitte → Rohrwand |
| εᵣ | Relative Dielektrizitätszahl |
| pF | Picofarad (10⁻¹² Farad) |

## Version

- **Aktuelle Version:** 1.0.0
- **Release-Datum:** 21. März 2026
- **APK:** `loop-break-7.0.0.apk`
