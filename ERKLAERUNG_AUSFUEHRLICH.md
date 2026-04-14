# Ausführliche Erklärung: Customer Analysis Notebooks

Dieses Dokument erklärt beide Notebooks Schritt für Schritt – was der Code macht, warum er so geschrieben ist, und was man daraus lernen kann.

---

## Was ist das Ziel dieser Notebooks?

Wir haben einen Datensatz eines Telekommunikationsunternehmens (ähnlich wie Vodafone). Darin stehen Informationen über Kunden – welche Verträge sie haben, wie lange sie schon Kunden sind, wie viel sie zahlen, und ob sie gekündigt haben.

Das **Ziel** ist: Verstehen, welche Kunden kündigen (Churn = Yes) und warum. Das ist ein klassisches Business-Problem – jedes Unternehmen mit Abonnements will wissen, wie es Kunden halten kann.

---

---

# NOTEBOOK 1: Datenanalyse mit Pandas

## Schritt 1 – Bibliothek importieren

```python
import pandas as pd
```

**Was ist Pandas?**  
Pandas ist die wichtigste Python-Bibliothek für die Arbeit mit Tabellendaten. Stell dir vor: Excel, aber in Python, und viel mächtiger. Mit Pandas kann man Tabellen laden, filtern, gruppieren, berechnen und transformieren.

---

## Schritt 2 – Datensatz laden

```python
df = pd.read_csv('customers.csv')
df.head(10)
```

**Was passiert hier?**  
`pd.read_csv()` liest eine CSV-Datei (kommagetrennte Tabelle) und lädt sie als sogenannten DataFrame – das ist die Hauptdatenstruktur in Pandas, ähnlich wie eine Tabelle in Excel.

`df.head(10)` zeigt die ersten 10 Zeilen. Das macht man immer zuerst, um zu sehen, wie die Daten aussehen.

**Was ist ein DataFrame?**  
Eine Tabelle mit Zeilen (= einzelne Kunden) und Spalten (= Eigenschaften wie Name, Umsatz, Churn).

---

## Schritt 3 – Größe und Struktur

```python
df.shape     # Wie viele Zeilen und Spalten?
df.columns   # Welche Spalten gibt es?
df.info()    # Datentypen und fehlende Werte
```

**Warum macht man das?**  
Bevor man irgendetwas analysiert, muss man verstehen, was man hat. `shape` gibt z.B. `(7043, 21)` zurück – also 7043 Kunden und 21 Spalten. `info()` zeigt außerdem, ob Daten fehlen und welchen Typ jede Spalte hat (Text, Zahl usw.).

---

## Schritt 4 – Fehlende Werte behandeln

```python
df_null = df[df.isnull().any(axis=1)]  # Zeilen mit fehlenden Werten anzeigen
```

**Was ist ein fehlender Wert (NaN)?**  
NaN = "Not a Number". Das bedeutet: an dieser Stelle fehlt ein Wert. Das passiert oft bei echten Daten, z.B. wenn ein Kunde keine TotalCharges hat, weil er ganz neu ist.

**Option 1: Zeilen löschen**
```python
df.dropna(inplace=True)
```
Einfach, aber man verliert Daten.

**Option 2: Fehlenden Wert ersetzen**
```python
df['TotalCharges'] = df['TotalCharges'].fillna(df['MonthlyCharges'])
```
Cleverer: Wenn TotalCharges fehlt (weil der Kunde neu ist), nehmen wir einfach den MonthlyCharges-Wert. Das ist logisch – ein neuer Kunde hat als Gesamtkosten ungefähr seinen ersten Monatsbeitrag.

**Warum ist das wichtig?**  
Fehlende Werte können Analysen und spätere Machine-Learning-Modelle kaputt machen. Man muss sie immer zuerst behandeln.

---

## Schritt 5 – Statistiken

```python
df.describe()
df['TotalCharges'].sum()
```

`describe()` gibt für alle numerischen Spalten automatisch Minimum, Maximum, Durchschnitt, Median usw. aus – ein schneller Überblick über die Datenlage.

`sum()` addiert alle Werte einer Spalte – hier: der gesamte Umsatz aller Kunden.

---

## Schritt 6 – Einzelne Kunden finden

```python
df.loc[df['TotalCharges'].idxmax()]   # Kunde mit höchstem Gesamtumsatz
df.loc[df['MonthlyCharges'].idxmax()] # Kunde mit höchstem Monatsbeitrag
df.loc[df['tenure'].idxmax()]         # Kunde mit längster Laufzeit
```

**Was macht `idxmax()`?**  
Es gibt den Index (die Zeilennummer) des größten Wertes zurück. Mit `df.loc[...]` rufen wir dann genau diese Zeile ab.

**Alternative Schreibweise:**
```python
df[df['TotalCharges'] == df['TotalCharges'].max()]
```
Der Unterschied: `idxmax()` gibt nur eine Zeile zurück (die erste mit dem Maximum). Die zweite Version gibt alle Zeilen zurück, die den Maximalwert haben – nützlich, wenn mehrere Kunden denselben Wert haben.

---

## Schritt 7 – Kategorien auswerten

```python
df['PhoneService'].value_counts()
df['InternetService'].value_counts()
df['Contract'].value_counts()
df['PaymentMethod'].value_counts()
df['Churn'].value_counts()
```

**Was macht `value_counts()`?**  
Es zählt, wie oft jeder einzigartige Wert in einer Spalte vorkommt. Beispiel: Bei `Contract` ergibt sich z.B. "Month-to-month: 3875, Two year: 1695, One year: 1473". Das zeigt sofort, dass die meisten Kunden Monatsverträge haben.

---

## Schritt 8 – Churn-Analyse: Gruppenvergleiche

```python
df.groupby('Churn').tenure.mean()
df.groupby('Churn').MonthlyCharges.mean()
df.groupby('Churn').TotalCharges.mean()
```

**Was ist `groupby`?**  
Es teilt den Datensatz in Gruppen auf (hier: Churn=Yes und Churn=No) und wendet dann eine Funktion an (hier: `mean()` = Durchschnitt).

**Was ergibt das?**  
Man sieht z.B., dass Kunden die kündigen (Churn=Yes) eine viel kürzere durchschnittliche Laufzeit haben als treue Kunden. Das ist ein wichtiger Insight.

**Aggregierte Form:**
```python
df.groupby('Churn').agg({
    'tenure': ['median'],
    'MonthlyCharges': ['mean'],
    'TotalCharges': ['mean']
})
```
Hier kann man für jede Spalte eine andere Funktion wählen (Median, Mittelwert, etc.) – praktisch für übersichtliche Zusammenfassungen.

---

## Schritt 9 – Mehrdimensionale Gruppenanalysen

```python
df.groupby(['Churn', 'gender']).size()
df.groupby(['Churn', 'InternetService']).size()
```

**Warum zwei Variablen gleichzeitig?**  
Man will wissen: Innerhalb der Churn-Gruppe – welche Untergruppen gibt es? Kündigen mehr Männer oder Frauen? Kündigen mehr Kunden mit DSL oder mit Glasfaser?

**Übersichtlichere Darstellung mit `.unstack()`:**
```python
churn_internet = df.groupby(['Churn', 'InternetService']).size().unstack()
```
`unstack()` verwandelt den zweiten Gruppierungs-Index (InternetService) in Spalten – das ergibt eine schöne Kreuztabelle.

**Prozentualer Anteil:**
```python
perc = churn_internet.div(churn_internet.sum(axis=1), axis=0) * 100
```
Das dividiert jede Zeile durch ihre Zeilensumme und multipliziert mit 100. Ergebnis: Prozentanteile statt absoluter Zahlen. Sehr wichtig für den Vergleich, weil es sonst auf die Gruppengröße ankommt.

---

## Schritt 10 – Filtern und Kundengruppen erstellen

```python
df_2 = df[
    (df['Churn'] == 'Yes') & 
    (df['PaymentMethod'] == 'Electronic check') & 
    (df['InternetService'] == 'Fiber optic')
]
```

**Was passiert hier?**  
Drei Filter gleichzeitig mit `&` (UND-Verknüpfung). Das ergibt eine spezifische Risikogruppe: Kunden die gekündigt haben, per Electronic Check bezahlen, und Glasfaser nutzen. Diese Gruppe hat offenbar besondere Eigenschaften.

**Spalten entfernen:**
```python
df_2 = df_2.drop(['Churn', 'PaymentMethod', 'InternetService'], axis=1)
```
Da alle Werte in diesen drei Spalten jetzt gleich sind (alle haben Yes/Electronic check/Fiber optic), braucht man sie nicht mehr – sie tragen keine Information mehr.

---

## Schritt 11 – Kundensegmentierung nach Umsatz

```python
def assign_group(TotalCharges):
    if TotalCharges < 220.6:
        return 'Group A'
    elif 220.6 <= TotalCharges < 952.3:
        return 'Group B'
    elif 952.3 <= TotalCharges < 2510.2:
        return 'Group C'
    else:
        return 'Group D'

df_2['CustomerGroup'] = df_2['TotalCharges'].apply(assign_group)
```

**Was ist hier das Ziel?**  
Die Kunden werden in 4 Umsatz-Segmente eingeteilt (A = niedrig, D = hoch). Diese Schwellenwerte basieren auf statistischen Quartilen aus `describe()`. Damit kann man z.B. sagen: Wie unterscheiden sich günstige Kunden (A) von wertvollen Kunden (D)?

**Was macht `.apply()`?**  
Es wendet eine Funktion auf jede Zeile (oder jeden Wert) einer Spalte an. Hier: für jeden Kunden wird die `assign_group`-Funktion aufgerufen und die Gruppe bestimmt.

---

## Schritt 12 – Feature Engineering

```python
group_counts = df_2.groupby('CustomerGroup').size().sort_values(ascending=False)
group_counts_df = group_counts.reset_index()
group_counts_df.columns = ['CustomerGroup', 'group_counts']
df_2 = pd.merge(df_2, group_counts_df, on='CustomerGroup', how='left')
```

**Was ist Feature Engineering?**  
Man erstellt neue Spalten aus vorhandenen Daten, die zusätzliche Information enthalten. Hier: Wir fügen jedem Kunden die Größe seiner Gruppe als neue Spalte hinzu. Das kann für spätere Analysen oder ML-Modelle nützlich sein.

**Was ist `merge`?**  
Verknüpfung zweier Tabellen über einen gemeinsamen Schlüssel – wie ein JOIN in SQL.

---

---

# NOTEBOOK 2: Datenvisualisierung

## Bibliotheken

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

**Matplotlib** ist die Grundlage für fast alle Plots in Python.  
**Seaborn** baut auf Matplotlib auf und macht es einfacher, schöne statistische Grafiken zu erstellen.  
**NumPy** wird hier vor allem für mathematische Operationen (z.B. `np.linspace`) genutzt.

---

## Subplots – mehrere Grafiken gleichzeitig

```python
fig, axes = plt.subplots(2, 3, figsize=(20, 10))
```

**Was ist das?**  
`subplots(2, 3)` erstellt ein Raster mit 2 Zeilen und 3 Spalten – also 6 Plots in einer Grafik. `figsize=(20, 10)` setzt die Gesamtgröße in Zoll. Das ist viel übersichtlicher als 6 einzelne Grafiken.

Jeder Plot wird dann über `axes[Zeile, Spalte]` angesprochen, z.B. `axes[0, 0]` für oben links.

---

## Histogramme für kategorische Variablen

```python
sns.histplot(data=df, x='gender', ax=axes[0, 0])
sns.histplot(data=df, x='Contract', hue='Churn', multiple='stack', ax=axes[1, 0])
```

**Was zeigt ein Histogramm hier?**  
Bei kategorischen Daten zeigt es Balken für jede Kategorie mit der Häufigkeit. `hue='Churn'` färbt die Balken nach Churn-Status ein. `multiple='stack'` stapelt sie – man sieht also auf einen Blick, wie viele aus jeder Vertragsart gekündigt haben.

**Wichtige Erkenntnis aus diesem Plot:**  
Kunden mit Monatsverträgen kündigen viel häufiger als Kunden mit 2-Jahres-Verträgen.

---

## Pie-Charts

```python
df.PaymentMethod.value_counts().plot(kind='pie', autopct='%.1f%%')
df[df["Churn"] == "Yes"]["InternetService"].value_counts().plot(kind="pie", autopct="%.1f%%")
```

**Wann ist ein Pie-Chart sinnvoll?**  
Wenn man Anteile (Prozent) darstellen will und es nicht zu viele Kategorien gibt. `autopct='%.1f%%'` zeigt den Prozentwert direkt im Kuchen-Segment an.

---

## Boxplots für numerische Variablen

```python
sns.boxplot(data=df, y='tenure', ax=axes[0])
sns.boxplot(data=df, y='MonthlyCharges', hue='Churn', ax=axes[1])
```

**Was zeigt ein Boxplot?**  
Ein Boxplot zeigt 5 Kennzahlen auf einmal:
- Untere Linie: Minimum (ohne Ausreißer)
- Untere Box-Kante: 25%-Quartil (25% der Werte liegen darunter)
- Mittlere Linie: Median (50%-Quartil)
- Obere Box-Kante: 75%-Quartil
- Obere Linie: Maximum (ohne Ausreißer)
- Punkte außerhalb: Ausreißer

**Mit `hue='Churn'`** sieht man zwei Boxplots nebeneinander – einen für Kunden die gekündigt haben, einen für loyale Kunden.

**Wichtige Erkenntnis:**  
Kunden die kündigen, haben höhere monatliche Gebühren und kürzere Laufzeiten.

---

## KDE-Plots (Kernel Density Estimation)

```python
sns.kdeplot(data=df, x='tenure', hue='Churn', fill=True)
```

**Was ist ein KDE-Plot?**  
Eine geglättete Kurve der Verteilung – ähnlich wie ein Histogramm, aber als kontinuierliche Kurve statt Balken. `fill=True` füllt den Bereich unter der Kurve aus.

**Vorteil gegenüber dem Histogramm:**  
Man sieht die Form der Verteilung klarer, unabhängig von der Wahl der Balkenbreite.

**Warum vergleicht man mit `hue='Churn'`?**  
Man sieht: Kunden die kündigen haben eine andere Tenure-Verteilung (viele kündigen früh), während loyale Kunden gleichmäßiger über die Zeit verteilt sind.

---

## ECDF – Empirische kumulative Verteilung

```python
sns.ecdfplot(data=df, x='tenure', stat="proportion")
```

**Was ist eine kumulative Verteilung?**  
Sie zeigt: Welcher Anteil der Kunden hat einen Wert kleiner oder gleich X? Am Punkt x=12 auf der x-Achse sagt die Kurve z.B. y=0.3, also: 30% der Kunden haben eine Laufzeit von maximal 12 Monaten.

**Warum nützlich?**  
Man sieht schnell, wie sich die Verteilung aufteilt – z.B. wie viel Prozent der Kunden in den ersten 6 Monaten kündigen.

---

## Korrelationsanalyse

```python
df[['tenure', 'MonthlyCharges', 'TotalCharges']].corr()
sns.heatmap(df[['tenure', 'MonthlyCharges', 'TotalCharges']].corr(), annot=True)
```

**Was ist Korrelation?**  
Ein Maß dafür, wie stark zwei Variablen zusammenhängen. Wert zwischen -1 und +1:
- +1 = perfekter positiver Zusammenhang (wenn A steigt, steigt B immer)
- 0 = kein Zusammenhang
- -1 = perfekter negativer Zusammenhang (wenn A steigt, fällt B immer)

**Was ergibt sich hier?**  
TotalCharges und tenure sind stark positiv korreliert – logisch, wer länger Kunde ist, hat mehr gezahlt. Das nennt man auch "triviale Korrelation" und man muss aufpassen, solche nicht falsch zu interpretieren.

**Was ist eine Heatmap?**  
Eine Tabelle, bei der die Werte als Farben dargestellt werden – dunklere Farbe = stärkere Korrelation. `annot=True` schreibt die Zahlenwerte zusätzlich in die Felder.

---

## Pairplot – die Gesamtübersicht

```python
sns.pairplot(df, hue="Churn", height=1.5)
```

**Was ist ein Pairplot?**  
Alle numerischen Variablen werden paarweise gegeneinander geplottet – ein Streudiagramm für jede Kombination. Auf der Diagonalen sieht man die Verteilung der einzelnen Variablen.

**Warum am Ende?**  
Weil es viele Plots auf einmal erzeugt und langsam ist. Man nutzt ihn am Ende für einen vollständigen Überblick oder um schnell Muster zu entdecken.

---

---

# Zusammenfassung: Was haben wir gelernt?

## Technisch

| Konzept | Pandas-Funktion |
|---|---|
| Datensatz laden | `pd.read_csv()` |
| Erste Zeilen anzeigen | `df.head()` |
| Fehlende Werte finden | `df.isnull()` |
| Fehlende Werte ersetzen | `df.fillna()` |
| Statistiken | `df.describe()` |
| Spalte aufsummieren | `df['col'].sum()` |
| Kategorien zählen | `df['col'].value_counts()` |
| Gruppieren | `df.groupby()` |
| Filtern | `df[bedingung]` |
| Spalten entfernen | `df.drop()` |
| Funktion anwenden | `df['col'].apply()` |
| Tabellen verbinden | `pd.merge()` |

## Inhaltlich (Business Insights)

1. **Monatsverträge = hohes Churn-Risiko** – Kunden ohne langfristige Bindung kündigen deutlich häufiger.
2. **Glasfaser-Internet = hohe Abwanderung** – möglicherweise wegen höherer Preise oder Qualitätsproblemen.
3. **Electronic Check = Warnsignal** – Kunden die per Electronic Check zahlen, kündigen häufiger als andere.
4. **Kurze Laufzeit = Risiko** – Kunden in den ersten 12 Monaten sind besonders abwanderungsgefährdet.
5. **Hohe monatliche Kosten + kurze Laufzeit** = die riskanteste Kombination.

Diese Insights könnte man bei Vodafone direkt nutzen, um Retention-Maßnahmen gezielt einzusetzen.
