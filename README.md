# loop break · Meldeader-Kapazität V1.03

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
Das heiße Medium (Wasser) im Inneren des Stahlrohrs liegt elektrisch
auf Stahlpotential, der Stahl wirkt aus Schaum-Sicht als Vollzylinder-Erdleiter.

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
  DN-Nummer wählen, Stahlaußendurchmesser und Wandstärke werden direkt in
  der Auswahlzeile angezeigt.
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

| Drahttyp | Leiter-ø | Iso-ø | Iso-Material | εᵣ Iso |
|----------|---------:|------:|--------------|-------:|
| Fühlerader NiCr80/20 | 0,50 mm | 1,10 mm | PTFE perforiert | 2,00 |
| Rückführader Cu     | 0,80 mm | 1,40 mm | PVC/PE          | 3,20 |

Die Fühlerader hat einen Querschnitt von 0,2 mm² (ρ ≈ 1,10 Ω·mm²/m, Widerstandsortung ±0,2 % der Messstrecke),
die Rückführader 0,5 mm². Beide Adern sind isoliert und bilden gemeinsam die Überwachungsschleife.

Das Brandes-System arbeitet nach dem Widerstands-Referenz-Messverfahren.
Vorteil: einheitliche Verdrahtung durch alle Hersteller, sehr genaue Ortung.

> **Wichtig zur Berechnung:** Bei Brandes-Adern berücksichtigt die App das
> **Zweischicht-Dielektrikum** aus Iso (innen) und PUR-Schaum (außen).
> Die Iso-Schicht wirkt als konzentrischer Zusatzkondensator in Serie zur
> exzentrischen PUR-Schicht. Effekt bei DN65/δ=15 mm: +6–7 % höhere C/L
> gegenüber blankem Leiter — die berechnete Länge wird entsprechend kürzer.

**KA-Tab – Ausführungskabel**

Dient zur Berechnung des Kapazitäts-Offsets für das Ausführungskabel.
Kabellänge, Aderquerschnitt (0,5 / 1,0 / 1,5 mm²) und Aderposition
(Benachbart / Übernächst / Gegenüber) eingeben → errechneter Offset
wird mit **„KA-Wert in Merk2"** direkt in Merk2 übertragen.

Kalibriert auf: **Cu/SMPE/VAS/FOL/AL+PE 4×2×0,8 mm**
(Kabel-Außendurchmesser 11,5 mm, PE-Mantel 1,75 mm, Al-Schirm 0,25 mm,
Schirminnendurchmesser 7,5 mm)

Empirisch gemessene C/m bei 0,5 mm² (5-m-Probestück, Eichwert 10 pF abgezogen):

| Aderposition | pF/m |
|--------------|-----:|
| Benachbart   | 43,0 |
| Übernächst   | 40,5 |
| Gegenüber    | 38,0 |

Diese Werte gelten gleichwertig für das baugleiche Kabel
SPL-EXYY / Radius-Kelit-Cubek-RFA 4×2×0,8 mm (identischer Aufbau und Außendurchmesser).

Für 1,0 und 1,5 mm² rechnet die App über die Zweidrahtleiter-Formel
(siehe Abschnitt **Berechnungsgrundlagen → KA-Offset**).

> **Hinweis:** Die geringe Streuung der drei Positionswerte (±5 pF/m)
> kommt daher, dass der Aluminiumschirm die Kapazität dominiert
> und der Aderabstand nur einen kleinen Beitrag liefert.

### Schritt 3 · Dämmstärke und Abstand δ wählen

Nach Rohrauswahl erscheinen vier Karten mit den möglichen Dämmstärken
(Premantrohr-⌀ und δ-Wert). **Dämmstärke 2** ist vorausgewählt (Standard).
Der Slider erlaubt freie Korrektur in 0,5-mm-Schritten; die Kartenauswahl
bleibt dabei unberührt. Slider und Kartenauswahl sind einseitig gekoppelt:
Karte wählen bewegt den Slider mit – Slider bewegen lässt die Karte unverändert.

Ein **★** auf einer Karte bedeutet: Die Min-15-Regel greift, weil der
geometrisch berechnete δ-Wert kleiner als 15 mm wäre und die Logstor-DS1-Standardgeometrie
(Distanzhalter hält Draht auf 15 mm vom Stahl) zuschlägt.

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

## Muffenkorrektur

Mindestens alle 10 m führt eine Brandes-Leitung durch eine Muffe. Die
Muffen-Distanzhalter halten den Draht auf einem **fixen Abstand** von
**δ = 19 mm** zur Rohrwand — unabhängig von der Dämmstärke des Streckenabschnitts.

In den Einstellungen kann der **Muffenanteil der Leitung in Prozent**
eingegeben werden. Die App berechnet dann zwei C/m-Werte parallel
(Streckenabschnitt mit aktuellem δ aus Slider/Karte, Muffenabschnitt mit fix δ=19 mm)
und mittelt sie gewichtet:

```
C_per_m_effektiv = (1 − a) · C_per_m_Strecke + a · C_per_m_Muffe
```

mit `a = Muffenanteil/100`.

**Effektgröße:** Bei DS3 (Strecken-δ ≈ 24 mm) und 10 % Muffenanteil verkürzt
sich die berechnete Länge um knapp 1 %. Bei DS1 mit aktiver Min-15-Regel
(Strecken-δ = 15 mm < 19 mm) wirkt die Korrektur umgekehrt — die berechnete
Länge wird leicht länger.

Default-Wert ist 0 % (keine Muffenkorrektur).

---

## Einstellungen

Über das ⚙-Symbol oben rechts erreichbar:

| Einstellung | Beschreibung |
|-------------|-------------|
| εᵣ | Dielektrizitätszahl PUR-Schaum (1,1 trocken bis 2,5 stark feucht) |
| Drahtdurchmesser | Messschieber-Istwert für alle Drahttypen (Leiter) |
| Brandes Iso-ø + εᵣ Iso | Iso-Außendurchmesser und εᵣ pro Brandes-Ader (parametrierbar) |
| Standard-Eichwert | Wird beim Öffnen automatisch ins Eichwert-Feld übertragen |
| Gerätefaktor K | Kalibrierfaktor Messgerät (Standard 1,000) |
| Feldfaktor | Empirischer Korrekturfaktor (Standard 1,000) |
| Muffenanteil (%) | Prozentanteil der Leitung in Muffen (Default 0) |
| Ausgabe | Meter oder Kilometer |

Drei kompakte Statusanzeigen unter dem Einstellungen-Knopf:

- **εᵣ x.xx** — aktueller Dielektrizitäts-Wert
- **Ff x.xx** — aktueller Feldfaktor
- **M-% x** — aktueller Muffenanteil

Alle Einstellungen werden lokal im Browser gespeichert (localStorage)
und bleiben nach dem Schließen erhalten. Einstellungen sind gerätespezifisch –
sie werden nicht zwischen Browser und Android-App synchronisiert.

---

## Hinweise zur Messung

- **Kapazitätsmessung nur bei Schleifenunterbruch** sinnvoll.
  Bei Feuchtigkeitsbrücke zwischen Ader und Rohr ist das Verfahren
  unzuverlässig (Parallelleitwert verfälscht den Messwert).
- **Messfrequenz 1 kHz** am Gerät einstellen.
- **εᵣ** bei trockenem, intaktem Schaum: 1,1–1,3.
  Bei Verdacht auf Alterung: 1,35–1,5 verwenden.
- **Verlustfaktor (D / tan δ) am LCR mitablesen.** Bei nassem oder
  feuchtem Schaum kann tan δ über 0,1 steigen — dann ist die
  Cs-Anzeige des LCR keine reine Kapazität mehr, sondern eine
  Mischgröße aus C und G. Bei tan δ > 0,5 sind die Werte unzuverlässig.
- Der **Eichwert** sollte mit angeschlossenem, aber nicht mit der
  Leitung verbundenem Kabel gemessen werden.
- **Parallelleiter-Effekt**: Bei Messungen über T-Stücke in parallele
  Leitungsabschnitte verfälscht der Parallelzweig den Messwert.
  Merk1/Merk2 erlauben die rechnerische Korrektur bekannter Teilstrecken.
- **Floating der zweiten Brandes-Ader** beeinflusst die Messung. Erden
  der ungemessenen Ader auf Stahlrohr **erhöht** die Kapazität (nicht senkt!),
  weil die geerdete Ader zum aktiven Mit-Erder nahe der Messader wird.

---

# Berechnungsgrundlagen und Materialdaten

Dieser Abschnitt dient als Nachschlagewerk für die in der App
hinterlegten Konstanten und Formeln.

---

## A · Hauptberechnung (Nordic / Brandes)

### Exzentrische Zwei-Zylinder-Formel

```
C/L = 2π · ε₀ · εᵣ / arcosh(−arg)
mit  arg = (R² + r² − e²) / (2·R·r)
und  e = R + δ + r
```

Bei Brandes wird `r` durch `r_iso` ersetzt (Iso-Außenradius wirkt als
elektrischer Leiterrand). Anschließend wird die konzentrische Iso-Schicht
als Serien-Kondensator hinzugerechnet:

```
C_iso/L = 2π · ε₀ · εᵣ_iso / ln(r_iso / r_leiter)
C_gesamt = (C_iso · C_pur) / (C_iso + C_pur)
```

### δ-Berechnung aus EN 253

Statt fester Tabellen-δ-Werte berechnet die App δ aus der Geometrie:

```
δ = max(15, (KMR_aussen − 2·t_PE − D_stahl) / 4)
```

Mit:
- `KMR_aussen` = gewählter KMR-Außendurchmesser
- `t_PE` = HDPE-Mantelstärke nach EN 253 (siehe Tabelle unten)
- `D_stahl` = Stahl-Außendurchmesser des DN-Typs

Die Min-15-Regel bildet die Logstor-DS1-Standardgeometrie ab, bei der
der Distanzhalter den Draht auf konstant 15 mm vom Stahl hält —
auch wenn der Schaumraum geometrisch enger wäre.

### EN-253-Mantelstärken (HDPE-Mindestwerte)

| KMR-Außen-ø | t_PE | KMR-Außen-ø | t_PE |
|---:|---:|---:|---:|
| 90 mm  | 3,0 mm | 280 mm  | 4,4 mm |
| 110 mm | 3,0 mm | 315 mm  | 4,9 mm |
| 125 mm | 3,0 mm | 355 mm  | 5,6 mm |
| 140 mm | 3,0 mm | 400 mm  | 6,3 mm |
| 160 mm | 3,0 mm | 450 mm  | 7,0 mm |
| 180 mm | 3,0 mm | 500 mm  | 7,8 mm |
| 200 mm | 3,2 mm | 560 mm  | 8,8 mm |
| 225 mm | 3,5 mm | 630 mm  | 9,8 mm |
| 250 mm | 3,9 mm |         |        |

---

## B · Brandes-Drahtdaten

| Parameter | Fühlerader NiCr | Rückführader Cu |
|---|---:|---:|
| Querschnitt | 0,20 mm² | 0,50 mm² |
| Leiterdurchmesser | 0,50 mm | 0,80 mm |
| Iso-Material | PTFE perforiert | PVC / PE |
| Iso-Wandstärke (beidseitig) | 0,30 mm | 0,30 mm |
| Iso-Außendurchmesser | 1,10 mm | 1,40 mm |
| εᵣ Iso (Default) | 2,00 | 3,20 |
| εᵣ-Bandbreite | 1,4 (perforiert) – 2,1 (massiv PTFE) | 2,3 (PE vernetzt) – 3,5 (PVC) |
| Spez. Widerstand Leiter | ρ ≈ 1,10 Ω·mm²/m | ρ ≈ 0,0175 Ω·mm²/m |

Iso-Außendurchmesser und εᵣ_iso sind in den Einstellungen pro Ader parametrierbar
(falls Datenblattwerte abweichen).

---

## C · KA-Kabel · Cu/SMPE/VAS/FOL/AL+PE 4×2

### Mechanische Daten

| Parameter | 0,5 mm² | 1,0 mm² | 1,5 mm² |
|---|---:|---:|---:|
| Aderdurchmesser Cu (Leiter) | 0,80 mm | 1,13 mm | 1,38 mm |
| Iso-Wandstärke (beidseitig) | 0,40 mm | 0,45 mm | 0,50 mm |
| Aderdurchmesser inkl. Iso | 1,60 mm | 2,03 mm | 2,38 mm |
| Aderisolierung εᵣ (SMPE/XLPE) | 2,3 | 2,3 | 2,3 |
| Al-Folienschirm Wandstärke | 0,25 mm | 0,25 mm | 0,25 mm |
| Schirm-Innendurchmesser | 7,5 mm | ~9,0 mm | ~10,5 mm |
| HDPE-Mantel Wandstärke | 1,75 mm | ~1,9 mm | ~2,1 mm |
| Kabel-Außendurchmesser | 11,5 mm | ~13,3 mm | ~15,0 mm |

> **Hinweis:** Schirm-, Mantel- und Außenmaße für 1,0 und 1,5 mm²
> sind aus 0,5-mm²-Daten extrapoliert (Datenblatt-Verifikation offen).

### KA-Berechnung — Zweidrahtleiter-Formel

Der KA-Tab nutzt die Zweidrahtleiter-Formel für zwei parallele isolierte Adern:

```
C/L = π · ε₀ · εᵣ / arcosh(D / d)
```

Mit:
- `d` = Leiter-Durchmesser (aus Querschnitt: d = √(4·A/π))
- `D` = Mitte-Mitte-Abstand der zwei Adern (mm)
- `εᵣ` = 2,3 (SMPE/XLPE-Aderisolierung)

### D-Werte aus 0,5-mm²-Messung rückgerechnet

Aus den drei empirischen Messwerten ergibt sich:

| Position | D (Mitte-Mitte) |
|---|---:|
| Benachbart | 1,86 mm |
| Übernächst | 2,02 mm |
| Gegenüber  | 2,23 mm |

### Skalierung auf 1,0 und 1,5 mm² — Modell C

Bei dickeren Adern berühren sich die Adern an ihren Iso-Außenflächen,
nicht am nackten Leiter. Die App skaliert D daher proportional zum
Iso-Außendurchmesser `d_iso`:

```
D_neu = D_05 · (d_iso_neu / d_iso_05)
```

Resultierende C/m-Werte:

| Position | 0,5 mm² | 1,0 mm² | 1,5 mm² |
|---|---:|---:|---:|
| Benachbart | 43,0 (gem.) | 46,7 (ger.) | 48,5 (ger.) |
| Übernächst | 40,5 (gem.) | 43,7 (ger.) | 45,2 (ger.) |
| Gegenüber  | 38,0 (gem.) | 40,8 (ger.) | 42,1 (ger.) |

Im Detail-Text der App wird mit „gemessen" oder „gerechnet" gekennzeichnet,
woher der Wert kommt.

> **Hinweis:** Die Werte für 1,0 und 1,5 mm² sind Modellvorhersagen, keine
> Messwerte. Sobald empirische Werte verfügbar werden, kann die Tabelle
> direkt mit Messwerten ersetzt werden.

---

## D · Muffenkorrektur

```
C_per_m_effektiv = (1 − a) · C_per_m_Strecke + a · C_per_m_Muffe
mit  a = Muffenanteil/100
und  C_per_m_Muffe berechnet mit fix δ = 19 mm
```

Begründung: Muffen-Distanzhalter halten den Draht in jeder Brandes-Muffe
auf konstant 19 mm vom Stahl. Auf Strecken mit anderer Dämmstärke
ist δ_Strecke ≠ δ_Muffe — diese geometrische Diskontinuität wird durch
gewichtete Mittelung der C/m-Werte abgebildet.

Die εᵣ-Werte in Muffen und Strecke werden in der aktuellen App-Version
gleich behandelt (PUR-Wert). Die Vergussmasse in Muffen hat
streng genommen ein anderes εᵣ — Effekt im niedrigen einstelligen Prozentbereich,
in der aktuellen Modell-Genauigkeit nicht relevant.

---

## E · Floating-Effekt der zweiten Brandes-Ader

Bei einer Brandes-Messung hängt die nicht-gemessene zweite Ader
elektrisch frei (floating). Sie nimmt ein Mittelpotential zwischen
Messader und Stahlrohr an und wirkt als kapazitiver Mit-Sinker.

**Aktueller App-Stand:** Der Floating-Effekt ist **nicht** explizit modelliert.
Er wird implizit über den Feldfaktor (`Ff`) abgefangen.

**Empirisch beobachtet:** Beim **Erden** der zweiten Ader auf Stahlrohr
**steigt** die gemessene Kapazität an (nicht sinkt, wie ältere Doku §4.2 vermutete).
Grund: Die geerdete Ader wird zum aktiven Mit-Erder direkt neben der
Messader und verkürzt den Feldweg drastisch.

**Größenordnung:** Bei normalem Distanzhalter-Aderabstand (4–6 mm)
ist der Floating-Effekt nach Doku-Tabelle ca. +16 % bei 5 mm Aderabstand.
Diese Werte basieren auf der Freiraum-Zweidrahtformel und sind nicht
für das geerdete-Stahlrohr-Umfeld kalibriert — die echte Größenordnung
muss durch Messung an trockenem Probestück mit bekannter Länge bestimmt werden.

> **Praxis-Hinweis:** Bei der Brandes-Messung die zweite Ader nicht erden.
> Dadurch werden Messwerte vergleichbar mit historischen Daten, und der
> Feldfaktor bleibt valide.

---

## F · Glossar

| Kürzel | Bedeutung |
|--------|-----------|
| FW | Fernwärme |
| VL / RL | Vorlauf / Rücklauf |
| MK | Messkreis |
| MKL | Messklemme |
| SÖ | Schadenöffnung |
| LUWA | Lecküberwachung |
| LMS | Leakage Monitoring System |
| HZ | Heizzentrale |
| KMR | Kunststoffmantelrohr (Premantrohr) |
| KA | Kabelausführung (Ausführungskabel) |
| DS | Dämmstärke |
| DN | Nennweite (Diameter Nominal) |
| δ | Abstand Drahtmitte → Rohrwand (mm) |
| εᵣ | Relative Dielektrizitätszahl |
| pF | Picofarad (10⁻¹² Farad) |
| TDR | Time Domain Reflectometry (Impulslaufzeit-Messverfahren) |
| SMPE / XLPE | Vernetztes Polyethylen (Aderisolierung) |
| PTFE | Polytetrafluorethylen (Teflonisolierung, perforiert bei NiCr-Ader) |
| HDPE | Hochdichtes Polyethylen (KMR-Mantel, KA-Kabel-Mantel) |
| tan δ / D | Verlustfaktor (Imaginär/Realteil bei LCR-Messung) |

---

## Kompatibilität

Die App läuft als einzelne HTML-Datei im Browser –
kein Server, keine Installation, keine Internetverbindung nötig.
Getestet auf Chrome/Android und Desktop-Browsern (Chrome, Firefox, Safari).

Für den Einsatz über GitHub Pages: Datei als `index.html` im Repository
ablegen und Pages unter *Settings → Pages* aktivieren.

Für die Android-APK: siehe `android-build-anleitung.md` im Repository.
