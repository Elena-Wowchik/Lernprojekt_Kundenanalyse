# Customer Analysis 

Zwei Jupyter Notebooks zur Analyse von Telekommunikations-Kundendaten mit Python.

## Datensatz

**Datei:** `customers.csv`  
Ein typischer Telekommunikations-Kundendatensatz mit folgenden Merkmalen:

| Spalte | Bedeutung |
|---|---|
| `gender` | Geschlecht des Kunden |
| `tenure` | Wie lange der Kunde schon Kunde ist (in Monaten) |
| `PhoneService` | Hat der Kunde einen Telefonvertrag? |
| `InternetService` | Art des Internetzugangs (DSL / Glasfaser / kein) |
| `Contract` | Vertragsart (monatlich / 1 Jahr / 2 Jahre) |
| `PaymentMethod` | Zahlungsmethode |
| `MonthlyCharges` | Monatliche Kosten |
| `TotalCharges` | Gesamtkosten |
| `Churn` | Hat der Kunde gekündigt? (Yes / No) |

---

## Notebook 1 – Datenanalyse mit Pandas

**Themen:**
- Datensatz laden und erkunden (`head`, `shape`, `info`, `describe`)
- Fehlende Werte erkennen und behandeln (löschen oder ersetzen)
- Einzelne Kunden analysieren (höchster Umsatz, längste Laufzeit)
- Kategorien auswerten (`value_counts`)
- Churn-Analyse: Unterschiede zwischen gekündigten und aktiven Kunden
- Kundengruppen erstellen und segmentieren

**Wichtigste Erkenntnis:** Kunden mit Monatsverträgen, Glasfaser-Internet und Electronic-Check-Zahlung zeigen die höchste Abwanderungsrate.

---

## Notebook 2 – Datenvisualisierung

**Themen:**
- Fehlende Werte bereinigen
- Histogramme und Balkendiagramme für kategorische Daten
- Pie-Charts für Anteile
- Boxplots für numerische Variablen
- Histogramme, KDE-Plots und ECDF für Verteilungen
- Alle Plots aufgeteilt nach Churn (Ja/Nein)
- Korrelationsmatrix und Heatmap
- Pairplot für Gesamtübersicht

**Bibliotheken:** `pandas`, `numpy`, `matplotlib`, `seaborn`

---

## Voraussetzungen

```bash
pip install pandas numpy matplotlib seaborn
```

---

## Ausführung

```bash
jupyter notebook
```

Notebooks in der Reihenfolge ausführen: erst `Customers.ipynb`, dann `Customer_Visualisierung.ipynb`.
