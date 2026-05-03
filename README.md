# loop break · Meldeader-Kapazität

Webanwendung zur Berechnung von Leitungslängen überwachter Fernwärmeleitungen
anhand von Kapazitätsmessungen an der Meldeader.

→ [github.com/MonkeyTor/loop-break](https://github.com/MonkeyTor/loop-break)

---

## Grundprinzip

Die App berechnet die Leitungslänge aus dem gemessenen Kapazitätswert (pF)
mithilfe der **exzentrischen Zwei-Zylinder-Formel** (EN 14419):

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

Getestet mit: Extech LCR200 (100 Hz / 120 Hz / 1 kHz / 10 kHz / 100 kHz).

---

## Vorgehensweise

### Schritt 1 · Mediumrohr bestimmen

Zwei Wege stehen zur Auswahl:

- **DN-Typ → Stahlrohr**: Rohrtyp aus Bestandsunterlagen bekannt →
  DN-Nummer wählen, Stahlaußendurchmesser wird automatisch gesetzt.
- **KMR-Außen-⌀**: Nur das Kunststoff-Premantrohr messbar →
  KMR-Durchmesser wählen. Die zugehörigen DN-Typen und Dämmstärken
  werden angezeigt.

Verfügbare Rohrtypen: DN20 bis DN500 nach EN-Norm.

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
| Fühlerader NiCr80/20 | Widerstandsdraht aus 80 % Nickel / 20 % Chrom, 0,2 mm², ø 0,50 mm. Perforierte Teflonisolierung lässt Feuchtigkeit an den Draht. Der hohe spezifische Widerstand (ρ ≈ 1,10 Ω·mm²/m) ermöglicht präzise Widerstandsortung (±0,2 % der Messstrecke). |
| Rückführader Cu | Vollisolierte Kupferader, 0,50 mm², ø 0,80 mm. Bildet gemeinsam mit der Fühlerader die Überwachungsschleife. Verbindung an jedem Rohrende. |

Das Brandes-System arbeitet nach dem Widerstands-Referenz-Messverfahren.
Vorteil: einheitliche Verdrahtung durch alle Hersteller, sehr genaue Ortung (±0,2 % der Messstrecke).

**KA-Tab – Ausführungskabel**

Dient zur Berechnung des Kapazitäts-Offsets für das Ausführungskabel.
Kabellänge, Aderquerschnitt (0,5 / 1,0 / 1,5 mm²) und Aderposition
(Benachbart / Übernächst / Gegenüber) eingeben → errechneter Offset
wird mit **„KA-Wert in Merk2"** direkt in Merk2 übertragen.

Kalibriert auf: **Cu/SMPE/VAS/FOL/AL+PE 4×2×0,8mm**
(Außendurchmesser 11,5 mm, PE-Mantel 1,75 mm, Al-Schirm 0,25 mm)

Gemessene Konstanten (netto, nach Eichwert-Abzug, Eichwert = 10 pF):

| Aderposition | pF/m |
|---|---|
| Benachbart | 43,0 |
| Übernächst | 40,5 |
| Gegenüber  | 38,0 |

Diese Werte gelten gleichwertig für das baugleiche Kabel
SPL-EXYY / Radius-Kelit-Cubek-RFA 4×2×0,8mm (identischer Aufbau und Außendurchmesser).

> **Hinweis:** Der Offset basiert auf direkten Feldmessungen an einem 5-m-Muster.
> Die drei Positionswerte streuen nur wenig (±5 pF/m), da der Aluminiumschirm
> die Kapazität gegenüber dem Aderabstand dominiert.

### Schritt 3 · Dämmstärke und Abstand δ wählen

Nach Rohrauswahl erscheinen vier Karten mit den möglichen Dämmstärken
(Premantrohr-⌀ und δ-Wert). **Dämmstärke 2** ist vorausgewählt (Standard).
Der Slider erlaubt freie Korrektur in 0,5-mm-Schritten; die Kartenauswahl
bleibt dabei unberührt. Slider und Kartenauswahl sind einseitig gekoppelt:
Karte wählen bewegt den Slider mit – Slider bewegen lässt die Karte unverändert.

### Schritt 4 · Messwerte eingeben

| Feld | Inhalt | Stellen |
|------|--------|---------|
| Kapazität gemessen | Messwert am Gerät (pF) | max. 5 |
| Eichwert | Gerät + offene Messkabel (pF) | max. 3 |
| Merk1 | Abgangs-Offset inkl. Eichwert (pF) | max. 5 |
| Merk2 | Abgangs-Offset exkl. Eichwert / KA-Offset (pF) | max. 4 |

**Eichwert**: Messung bei offenem Kabel ohne angeschlossene Leitung.

**Merk1-Knopf**: Friert den aktuellen C-Wert mit allen aktiven Parametern
(Rohrtyp, Draht, δ, εᵣ) zum Zeitpunkt des Drückens ein. Der eingefrorene
Meterwert bleibt unverändert auch wenn danach andere Parameter gewählt werden.

**Merk2-Knopf**: Wie Merk1, jedoch ohne Eichwert-Abzug. Kann auch direkt
über den KA-Tab befüllt werden.

### Schritt 5 · Länge berechnen

Schaltfläche **„Länge berechnen ↗"** auslösen.
Nicht verfügbar wenn der KA-Tab aktiv ist.

Das Ergebnis erscheint im gelben Feld:
- **Hauptwert**: berechnete Gesamtlänge
- **Abgang 1**: eingefrorene Länge aus Merk1 (Zeitpunkt „Merk1")
- **Abgang 2**: eingefrorene Länge aus Merk2 oder KA-Kabellänge

Die Hauptberechnung zieht **Merk1 und Merk2 gleichzeitig** ab,
sofern beide Felder befüllt sind:

```
C_netto = (C_gemessen − Merk1 − Merk2) · K_gerät · K_feld
```

---

## Merkfelder – Workflow Leitungsabschnitte

Typischer Anwendungsfall: Messung über einen Hausabgang in die Hauptleitung,
wobei sich Rohrtyp, Drahttyp oder Dämmstärke am T-Stück ändern.

1. **Abgangsparameter einstellen** (Rohrtyp, Draht, δ des Abgangs)
2. C-Wert so anpassen, bis die bekannte Abgangslänge angezeigt wird
3. **„Merk1"** drücken → Wert wird eingefroren (inkl. Eichwert)
4. Rohrtyp und Draht auf Hauptleitung umstellen
5. Gesamtmessung eingeben → **„Länge berechnen ↗"**
6. Ergebnis = reine Distanz vom T-Stück zur Schaltstelle

---

## KA-Offset-Workflow

Typischer Anwendungsfall: Messung mit Ausführungskabel an der Kabelausführung.

1. **KA-Tab** auswählen
2. Aderquerschnitt, Kabellänge und Aderposition einstellen
3. Berechneter Offset erscheint automatisch
4. **„KA-Wert in Merk2"** drücken → Offset wird in Merk2 übertragen,
   Kabellänge erscheint im Abgang-2-Feld
5. Zurück zu Nordic oder Brandes wechseln
6. Messung wie gewohnt durchführen – KA-Offset wird automatisch abgezogen

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
und bleiben nach dem Schließen erhalten. Einstellungen sind gerätespezifisch –
sie werden nicht zwischen Browser und Android-App synchronisiert.

---

## Hinweise zur Messung

- **Kapazitätsmessung nur bei Schleifenunterbruch** sinnvoll.
  Bei Feuchtigkeitsbrücke zwischen Ader und Rohr ist das Verfahren
  unzuverlässig (Parallelleitwert verfälscht den Messwert).
- **Messfrequenz 1 kHz** am Gerät einstellen (nicht 100 Hz, nicht 10 kHz).
  100 Hz liegt auf der zweiten Netzoberwelle (europäisches Netz 50 Hz).
  10 kHz bringt bei den typischen Leitungslängen keinen Vorteil.
- **εᵣ** bei trockenem, intaktem Schaum: 1,1–1,3.
  Bei Verdacht auf Alterung: 1,35–1,5 verwenden.
- Der **Eichwert** sollte mit angeschlossenem, aber nicht mit der
  Leitung verbundenem Kabel gemessen werden.
- **Parallelleiter-Effekt**: Bei Messungen über T-Stücke in parallele
  Leitungsabschnitte verfälscht der Parallelzweig den Messwert.
  Merk1/Merk2 erlauben die rechnerische Korrektur bekannter Teilstrecken.

---

## Kompatibilität

Die App läuft als einzelne HTML-Datei im Browser –
kein Server, keine Installation, keine Internetverbindung nötig.
Getestet auf Chrome/Android und Desktop-Browsern (Chrome, Firefox, Safari).

Für den Einsatz über GitHub Pages: Datei als `index.html` im Repository
ablegen und Pages unter *Settings → Pages* aktivieren.

Für die Android-APK: siehe `android-build-anleitung.md` im Repository.

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
| DN | Nennweite (Diameter Nominal) |
| δ | Abstand Drahtmitte → Rohrwand |
| εᵣ | Relative Dielektrizitätszahl |
| pF | Picofarad (10⁻¹² Farad) |
| TDR | Time Domain Reflectometry (Impulslaufzeit-Messverfahren) |
| SMPE | Vernetztes Polyethylen (Aderisolierung) |
