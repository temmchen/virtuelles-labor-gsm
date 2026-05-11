# Virtuelles Labor – Fremderregter Gleichstrommotor (GSM)

Interaktive Browser-Simulation zur Aufnahme von **Belastungskennlinien** eines fremderregten Gleichstrommotors. Schülerinnen und Schüler durchlaufen den kompletten Laborablauf — Theorie, Antestat, Sicherheits-Check, Messreihe, Vorhersage, Auswertung und PDF-Bericht — bevor sie an die echte Maschine gehen.

> 📚 ELTEC4n – DP2ET • ⏱ 4 × 50 min • 🏫 Lycée Technique Ettelbruck

---

## ✨ Features

- **8-Phasen-Workflow** mit Tab-Navigation und Fortschrittssperre (jede Phase muss abgeschlossen sein, bevor die nächste freigeschaltet wird)
- **Live-Simulation** des Motors: Erregerstrom I_F, Ankerspannung U_A und Bremsmoment M sind über Schieber einstellbar; n, I_A und P_el werden in Echtzeit berechnet
- **Sicherheits-Check** mit korrekter Hochfahr- und Herunterfahr-Reihenfolge (Erregung vor Anker, Anker vor Erregung herunter) — Fehlschaltungen werden geloggt
- **Antestat (Quiz)** mit 5 Fragen vor Laborzugang
- **Vorhersage-Phase**: Lernende prognostizieren den Verlauf der drei Kennlinien, bevor sie ihre Messdaten sehen
- **3 automatisch erzeugte Diagramme**: n = f(M), I_A = f(M), P_el = f(M) (Chart.js)
- **PDF-Laborbericht** mit Identifikation, Messtabelle, Diagrammen, Vorhersagen, Auswertung und vollständigem Event-Log (jsPDF)
- **Lehrermodus** (passwortgeschützt) blendet Musterlösungen in jedes Diagramm und in jede Auswertefrage ein
- **Vollständig clientseitig**: keine Backend-Abhängigkeit, kein Build-Schritt, läuft als einzelne `index.html`

---

## 🚀 Bedienung

```bash
git clone https://github.com/temmchen/virtuelles-labor-gsm.git
cd virtuelles-labor-gsm
open index.html        # macOS
# oder einfach im Browser öffnen
```

Optional über einen lokalen Server, falls der Browser CORS-Restriktionen hat:

```bash
python3 -m http.server 8000
# dann http://localhost:8000 aufrufen
```

Es gibt **keine Build-Tools** und keine Abhängigkeiten zu installieren. Chart.js und jsPDF werden zur Laufzeit von cdnjs geladen.

---

## 🧭 Die 8 Phasen

| # | Phase | Zweck |
|---|---|---|
| 0 | 📖 Start | Identifikation (Name, Klasse, Gruppe) — Pflicht für PDF |
| 1 | 1️⃣ Theorie | Wichtige Formeln, Verhalten bei Belastung |
| 2 | 2️⃣ Antestat | 5-Fragen-Quiz, mind. 4 richtig zum Bestehen |
| 3 | 3️⃣ Sicherheits-Check | Korrekte Schaltreihenfolge erlernen |
| 4 | 4️⃣ Virtuelles Labor 🧪 | 7 Messpunkte (M = 0,0 … 1,2 N·m) aufnehmen |
| 5 | 5️⃣ Vorhersage 🔮 | Verlauf der drei Kennlinien prognostizieren + begründen |
| 6 | 6️⃣ Auswertung | Diagramme einsehen, Auswertefragen vorbereiten |
| 7 | 7️⃣ Bericht (PDF) | Vollständiger Laborbericht als PDF-Download |
| 8 | 📋 Labor-Checkliste | Was vor dem realen Versuch noch zu tun ist |

---

## 🧮 Modellgleichungen

Im Versuch werden für den fremderregten Gleichstrommotor folgende Beziehungen genutzt:

| Größe | Gleichung |
|---|---|
| Ankerspannung | U_A = R_A · I_A + U_i |
| Induzierte Spannung | U_i = c · Φ · n |
| Drehmoment | M = c · Φ · I_A |
| Drehzahl | n = (U_A − R_A · I_A) / (c · Φ) |
| **Aufnahmeleistung** | **P_el = U_A · I_A + I_F · U_F** |

Die Aufnahmeleistung umfasst sowohl den **Anker-** als auch den **Erregerkreis**. In der Form `y = a·M + b` ergibt das einen linearen Verlauf mit dem **konstanten Achsenabschnitt b = I_F · U_F** und der Steigung `a = U_A · (1 / (c·Φ))`.

> Der Wert für U_F lässt sich am Anfang des Skript-Blocks in `index.html` über die Konstante `U_F` setzen (Default: 220 V — entspricht separater Erregung am gleichen Potential wie der Anker).

### Nominelle Betriebspunkte

| Parameter | Wert |
|---|---|
| U_A nominal | 220 V |
| I_F nominal | 0,35 A |
| R_A | 8,0 Ω |
| Drehmomentbereich Versuch | 0,0 – 1,2 N·m (7 Punkte) |
| Drehzahl im Leerlauf | ≈ 2500 1/min |

---

## 👨‍🏫 Lehrermodus

Lehrkräfte können einen **Lehrermodus** aktivieren, der in den Diagrammen und in der Auswertung Musterlösungen einblendet:

- Aktivierung: `?teacher=<passwort>` an die URL anhängen, oder über das Profil-Menü
- Standard-Passwort und -Email werden über die Konstanten `TEACHER_PWD` und `TEACHER_EMAIL` in `index.html` (oben im JavaScript-Block) gesetzt — vor dem Verteilen ggf. anpassen
- Sichtbar wird ein orangefarbenes Badge oben rechts; ein Klick deaktiviert den Modus wieder

---

## 🔧 Konfiguration

Alle anpassbaren Parameter stehen im JavaScript-Block in `index.html` direkt unter den Skill-Strings:

```js
const TEACHER_PWD = "gsm";
const TEACHER_EMAIL = "tom.bleyer@me.com";
const RA = 8.0;          // Anker-Widerstand [Ω]
const U_F = 220;         // Erregerspannung [V] – bei separater Erregung typisch = U_A
```

Die nominellen Sollwerte (220 V Anker, 0,35 A Erregung) und die 7 Soll-Messpunkte (Δ = 0,2 N·m) sind in `updateSim()` und in `buildCharts()` hinterlegt.

---

## 📦 Tech-Stack

- **HTML / CSS / Vanilla JavaScript** — keine Frameworks
- **[Chart.js 4.4.0](https://www.chartjs.org/)** für die Belastungskennlinien
- **[jsPDF 2.5.1](https://github.com/parallax/jsPDF)** für den Laborbericht-Export
- Single-File-Deployment — `index.html` ist alles

---

## 📂 Struktur

```
.
├── index.html      # gesamte Anwendung (Markup + Style + Logik)
├── README.md       # dieses Dokument
└── .gitattributes
```

---

## 🤝 Beiträge

Pull Requests sind willkommen — insbesondere für:

- weitere Antestat-Fragen
- alternative Maschinenparameter (z. B. andere Lab-Setups)
- Übersetzungen (FR / LU)
- zusätzliche Auswertegrafiken

---

## 📝 Lizenz / Nutzung im Unterricht

Erstellt für den Einsatz im Modul **ELTEC4n** am **Lycée Technique Ettelbruck**. Verwendung im schulischen Kontext frei; bei Weitergabe bitte Quelle nennen.

---

_Letzte fachliche Korrektur: P_el-Berechnung um den konstanten Erreger-Anteil `I_F · U_F` ergänzt (vorher nur `U_A · I_A`)._
