#Customer Churn Analysis – Telekommunikation

> **Ziel:** Identifikation von Mustern bei abgewanderten Kunden (Churn) eines Telekommunikationsunternehmens mithilfe von Python (Pandas, Matplotlib, Seaborn).

---

## Key Metrics

![KPI Banner](images/00_kpi_banner.png)

| Metrik | Wert |
|---|---|
| Datensatz | 7.043 Kunden |
| Churn-Rate | 26,5% |
| Ø Laufzeit bei Churn | 18 Monate |
| Ø Laufzeit treu | 37 Monate |
| Glasfaser-Churn | 42% |
| Monatsvertrag-Churn | 43% |

---

## Technologien

Python
Pandas
Seaborn
Matplotlib

---

## Datensatz

`customers.csv` – Kundendaten eines Telekommunikationsunternehmens

| Spalte | Typ | Beschreibung |
|---|---|---|
| `tenure` | Numerisch | Vertragslaufzeit in Monaten |
| `MonthlyCharges` | Numerisch | Monatliche Kosten |
| `TotalCharges` | Numerisch | Gesamtkosten |
| `Contract` | Kategorie | Monatlich / 1 Jahr / 2 Jahre |
| `InternetService` | Kategorie | DSL / Glasfaser / Kein |
| `PaymentMethod` | Kategorie | 4 Zahlungsmethoden |
| `Churn` | Ziel-Variable | Yes / No |

---

## Analyse & Ergebnisse

### 1 — Churn-Überblick

![Churn Overview](images/01_churn_overview.png)

**Erkenntnisse:**
- Jeder 4. Kunde kündigt – Churn-Rate von **26,5%**
- Monatsverträge haben mit **43% Churn-Rate** das höchste Risiko – fast 4× höher als 2-Jahres-Verträge (11%)
- Glasfaser-Kunden kündigen mit **42%** deutlich häufiger als DSL-Kunden (19%)

---

### 2 — Numerische Unterschiede: Churn vs. Treu

![Boxplots](images/02_boxplots_churn.png)

**Erkenntnisse:**
- Abgewanderte Kunden haben eine **deutlich kürzere Vertragslaufzeit** (Median ~10 Monate vs. ~38 Monate)
- Churn-Kunden zahlen **höhere monatliche Gebühren**
- Treue Kunden haben höhere Gesamtkosten, weil sie einfach länger dabei sind

---

### 3 — Verteilungen: Wann kündigen Kunden?

![KDE Distributions](images/03_kde_distributions.png)

**Erkenntnisse:**
- Kündiger häufen sich stark in den **ersten 12 Monaten** – der Onboarding-Zeitraum ist kritisch
- Kündiger haben eine Konzentration bei **hohen monatlichen Kosten** (> 70€)
- Treue Kunden zeigen eine gleichmäßigere Verteilung über alle Laufzeiten

---

### 4 — Korrelation & Zahlungsmethoden

![Correlation and Payment](images/04_corr_payment.png)

**Erkenntnisse:**
- `TotalCharges` und `tenure` korrelieren stark (0.83) – logisch: je länger Kunde, desto höhere Gesamtkosten
- **Electronic Check** ist ein Risikomerkmal: 45% Churn-Rate – mehr als doppelt so hoch wie bei automatischer Zahlung (15–17%)

---

## Die Risiko-Kombination

Kunden mit allen drei dieser Merkmale zeigen die höchste Abwanderungswahrscheinlichkeit:

```
Monatsvertrag  +  Glasfaser  +  Electronic Check  →  ~45% Churn-Rate
```

**Business-Empfehlung:** Frühzeitige Retention-Maßnahmen für neue Kunden (< 12 Monate) mit dieser Kombination – z.B. Angebot eines Jahresvertrags mit Rabatt oder Umstieg auf automatische Zahlung.

---

## Projektstruktur

```
customer-churn-analysis/
│
├── README.md
├── customers.csv
├── 01_Customers_EDA.ipynb
├── 02_Customers_Visualisierung.ipynb
└── images/
    ├── 00_kpi_banner.png
    ├── 01_churn_overview.png
    ├── 02_boxplots_churn.png
    ├── 03_kde_distributions.png
    └── 04_corr_payment.png
```

---

## Setup

```bash
git clone https://github.com/Elena-Wowchik/customer-churn-analysis
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook
```

---

*Analyse durchgeführt im Rahmen der Weiterbildung Data Science & Projektmanagement – DATA SCIENCE Institute, Berlin 2025*
