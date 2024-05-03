# Deskriptive Statistik

## Absolute und Relative Häufigkeiten

**Absolute und Relative Häufigkeit**

 - Die absolute Häufigkeit ist die Anzahl (= ganze Zahl) wie oft ein Merkmal in einer Stichprobe vorkommt.
 - Die relative Häufigkeit hingegen ist der Anteil den eine Merkmalsauspräung in einer Stichprobe ausmacht.

### Absolute Häufigkeiten

Absolute Häufigkeiten können durch `table()` in einer Tabelle ausgegeben werden:  

\bigskip\small


```r
df_yoga <- read.table("data/YogaPilates.txt", header = TRUE)
```


```r
tab <- table(df_yoga$gruppe)
tab

kontroll  pilates     yoga 
      45       46       29 
```

Die Gesamtzahl der Beobachtungen kann mit `sum()` ausgegeben werden:

\bigskip


```r
sum(tab)
[1] 120
```

### Relative Häufigkeiten

2 Möglichkeiten: \
1. Berechnung der relativen Häufigkeit durch Divsion der absoluten Häufigkeiten mit der Gesamtzahl der Beobachtungen.  
2. Verwenden der Funktion `prop.table()`.


```r
tab_rel <- tab/sum(tab)

tab_rel <- prop.table(tab)


 kontroll   pilates      yoga 
0.3750000 0.3833333 0.2416667 
```

### Kreuztabelle für mehrere Variablen

- Wie ist die (absolute und relative) Häufigkeit der Frauen und Männer (`geschl`) in den einzelnen Gruppen (`gruppe`)?  
- Bei Angabe von mehreren Vektoren werden Kreuztabellen erzeugt, dessen Werte mit `round()` gerundet werden können:


```r
tab2 <- table(df_yoga$geschl, df_yoga$gruppe)
round(prop.table(tab2), 2)
   
    kontroll pilates yoga
  m     0.12    0.10 0.05
  w     0.26    0.28 0.19
```

## Grundbefehle  

R-Befehl  | Bedeutung
----------|----------
`sum()`     |Summe
`mean()`   |Mittelwert
`var()`     |Varianz
`sd()`      |Standardabweichung
`min()`     |Minimum
`max()`     |Maximum
`quantile()`|Quartile
`range()`   |Spannweite
`median()`  |Median

**Beispiele**


```r
range(df_yoga$alter)
[1] 21 40
mean(df_yoga$alter)
[1] 30.73333
var(df_yoga$zufri)
[1] NA
```

**Fehlende Werte**

Enthalten Daten fehlende Werte (`NA`), dann ergeben die deskriptiven Berechnungen auch `NA`. Durch das Argument `na.rm = TRUE` werden `NA`-Werte ignoriert:


```r
mean(df_yoga$zufri)
[1] NA
mean(df_yoga$zufri, na.rm = TRUE)
[1] 3.543103
```

**Funktion summary()**

- Mit `summary()` werden verschiedene deskriptive Statistiken ausgegeben:


```r
summary(df_yoga$alter)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  21.00   26.00   30.50   30.73   35.25   40.00 
```

- `summary()` gibt zudem die Anzahl fehlender Werte an:


```r
summary(df_yoga$zufri)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
  1.000   3.000   4.000   3.543   4.000   5.000       4 
```

**Paket psych**

Mit der Funktion `describe()` aus dem Paket **psych** lassen sich eine Vielzahl von Verteilungsparametern gleichzeitig ausgeben:



```r
library(psych)
describe(df_yoga$alter, skew = FALSE)
   vars   n  mean   sd median min max range   se
X1    1 120 30.73 5.76   30.5  21  40    19 0.53
```

Optionale Argumente:  
- `skew = FALSE`, um Schiefe und Kurtosis nicht auszugeben  
- `ranges = FALSE`, um u.a. Range, Minimum und Maximum nicht auszugeben  
- `IQR = TRUE`, um Interquartilbereich auszugeben

## Gruppengetrennte Analyse

Wie können wir für die Variablen `alter`, `zufri` und `angst` deskriptive Statistien berechnen, je nachdem in welcher `gruppe` die Person ists?


```r
head(df_yoga, 5)
    vp geschl alter  gruppe zufri angst
1 AA21      w    37    yoga     5     1
2 AW14      m    31 pilates     4     4
3 BA55      w    38    yoga     4     2
4 BA76      m    35    yoga     5     2
5 BP45      w    23 pilates     4     1
```

### Logisches Indizieren

Jeweils Teile der Daten durch logisches Indizieren auswählen:     
„Berechne den Mittelwert der Spalte `alter`, aber wähle hierfür nur Werte der Personen aus...

- ...`gruppe == yoga`.“



```r
mean(df_yoga$alter[df_yoga$gruppe == "yoga"])
[1] 31.13793
```

- ...`gruppe == pilates`."


```r
mean(df_yoga$alter[df_yoga$gruppe == "pilates"])
[1] 30.86957
```

### Funktion aggregate()

Mit `aggregate()` können Funktionen für verschiedene Faktorenstufen (und deren Kombination) getrennt berechnet werden\

- `aggregate(AV ~ UV, FUN = …, data = …)`\
    - `AV`: Variable, deren Werte analysiert werden sollen  
    - `UV`: Faktor(en), mehrere Faktoren werden mit + verbunden  
    - `FUN`: Welche Funktion (`length, sum, mean, ...`) soll berechnet werden?  
    - `data`: Datensatz  
  
**Beispiel**

Durchschnittliche Zufriedenheit, Gruppen = geschl, gruppe


```r
aggregate(zufri ~ geschl + gruppe, FUN = mean, data = df_yoga)
  geschl   gruppe    zufri
1      m kontroll 3.785714
2      w kontroll 3.258065
3      m  pilates 3.181818
4      w  pilates 3.454545
5      m     yoga 4.166667
6      w     yoga 3.952381
```

### Funktion describeBy()

- aus dem Paket *psych*  
- Anwendung der describe-Funktion getrennt nach Faktor(en)  
- `describeBy(x = …, group = list(…), …)`  
    - `x`: Variable, deren Werte analysiert werden sollen (wie AV bei `aggregate()`)
    - `group = list()`: Faktor(en)

**Beispiel describeBy()**

Deskriptive Statistiken zu Zufriedenheit nach Gruppe (= `geschl`)


```r
describeBy(x = df_yoga$zufri, group = list(df_yoga$geschl), skew = FALSE)

 Descriptive statistics by group 
: m
   vars  n mean   sd median min max range   se
X1    1 31 3.65 0.95      4   2   5     3 0.17
--------------------------------------------- 
: w
   vars  n mean   sd median min max range   se
X1    1 85 3.51 1.06      4   1   5     4 0.12
```

## Ergänzungen

### Bedingte Wahrscheinlichkeiten

Mit `prop.table(…, margin = …)` werden bedingte Wahrscheinlichkeiten für ein Merkmal ausgegeben. (margin 1 = zeilenweise, margin 2 = spaltenweise)

Bedingte Wahrscheinlichkeit, zeilenweise, gerundet auf 2 Stellen


```r
round(prop.table(tab2, margin = 1), 2)
   
    kontroll pilates yoga
  m     0.44    0.38 0.19
  w     0.35    0.39 0.26
```

Bedingte Wahrscheinlichkeit, spaltenweise, gerundet auf 2 Stellen


```r
round(prop.table(tab2, margin = 2), 2)
   
    kontroll pilates yoga
  m     0.31    0.26 0.21
  w     0.69    0.74 0.79
```


### Skalieren

- Mit dem Befehl `scale(…, center = …, scale = …)` können Variablen zentriert und z-standardisiert werden.
  - `center`: Soll von jedem Wert in der Variable der Variablenmittelwert abgezogen werden? (TRUE =  ja)\
  - `scale`: Soll jeder Wert in der Variable durch die Variablenstandardabweichung dividiert werden? (TRUE = ja)\
  - Zentrierung, wenn nur `center = TRUE`, z-Standardisierung, wenn beide Argumente mit TRUE definiert wurden.

### Extremwerte

- Mit dem Argument `trim = …` im Befehl `mean()` kann der Anteil ausgeschlossener Extremwerte definiert werden

- Das Paket `moments` gibt mit dem Befehl `all.moments()` alle statistischen Momente aus, mit `skewness()` die Schiefe und mit `kurtosis()` den Exzess

### Gruppengetrennte Analyse - Funktion tapply()

`tapply(X = …, INDEX = list(…), FUN = …)`

  - `X`: Variable, deren Werte analysiert werden sollen (wie AV bei `aggregate()`)
  - `INDEX = list()`: Faktor(en), mehrere Faktoren werden mit "," verbunden (wie UV bei `aggregate()`)
    - Bei einem Faktor muss `list()` nicht angegeben werden
  - `FUN`: Welche Funktion (deskriptive Statistik) soll berechnet werden? (wie bei `aggregate()`)
