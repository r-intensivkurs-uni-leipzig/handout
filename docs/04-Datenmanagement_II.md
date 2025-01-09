# Datenmanagement II

## Datenbearbeitung

- Wir wissen nun wie Daten eingelesen und zusammengefügt werden
- Bevor wir mit unserem Datensatz Statistik betreiben können, müssen aber meist noch weitere Schritte der Datenbearbeitung durchgeführt werden
- Beispieldatensatz:


``` r
personenDaten <- read.table("data/PersonenDaten.txt", header = TRUE)
head(x = personenDaten, n = c(5, 12))
  Person item1 item2 item3 item4 item5 item6 item7 item8
1      1     3     2     3     2     2     1     4     1
2      2     4     1     4     3     1     1     4     1
3      3     3     1     3     3     1     1     3     1
4      4     2     2     2     2     2     2     2     2
5      5     2     2     2     2     1     1     3     2
  item9 item10 item11
1     1      3      4
2     1      4      2
3     1      3      4
4     2      2      4
5     2      2      2
```

### Umkodierung

**Situation:** Im `personDaten`- Datensatz ist die Variable `sex` mit 1 und 2 kodiert. Die Kodierung soll aber 0 und 1 sein.
 
Der `$`- Operator schafft hier abhilfe wenn...

- eine neue Variable aus einer alten erstellt werden soll


``` r
personenDaten$sexUmkodiert <- personenDaten$sex - 1
personenDaten$sexUmkodiert
 [1] 1 0 0 1 0 1 1 1 0 0 1 1 1 1 1 0 0 1 0 1
```

- eine existierende Variable überschrieben werden soll


``` r
personenDaten$sex <- personenDaten$sex - 1
personenDaten$sex
 [1] 1 0 0 1 0 1 1 1 0 0 1 1 1 1 1 0 0 1 0 1
```

**Situation:** Einige Items im Datensatz sind umgepolt wurden. Für die weitere Verarbeitung sollen sie zurückgepolt werden.

- Anwendung der Formel:  $Item _{Umkodiert} = max(Item)+1 -Item _{Original}$

- Beispiel: Umpolung von Item3 mit 6-stufiger Skala


``` r
personenDaten$item3_umkodiert <- max(personenDaten$item3) + 1 - personenDaten$item3
```

```
  Original Umkodiert
1        3         4
2        4         3
3        3         4
4        2         5
5        2         5
```

### Dichotomisierung

**Situation:** Das Alter der Personen soll in eine Dummy Variable überführt werden, bei der alle maximal 30 jährigen den Wert 0 und alle älter als 30 den Wert 1 haben sollen.

`ifelse(test, yes, no)` liefert die Möglichkeit, R-Befehle abhängig von einer Bedingung auszuführen

- `condition`: Bedingung, die überprüft werden soll
- `TRUE`: Welcher Wert soll zugewiesen werden, wenn die Bedingung erfüllt ist?
- `FALSE`: Welcher Wert soll zugewiesen werden, wenn die Bedingung nicht erfüllt ist?


``` r
personenDaten$alterDichotom <- ifelse(personenDaten$age <= 30, 0, 1)
```

Überprüfung

```
  Original Umkodiert
1       25         0
2       27         0
3       38         1
4       31         1
5       22         0
```

### Kategorisierung

**Situation:** Das Alter der Personen soll in eine weitere Variable Altersgruppen überführt werden, bei der:
 - alle unter 25 den Wert 1
 - alle von 25 bis 30 den Wert 2
 - alle von 30 bis 35 den Wert 3
 - alle über 35 den Wert 4 erhalten

Logisches Indizieren ist ein einfacher Weg, Variablenwerte abhängig von Bedingungen festzusetzen: `dataset$neueVariable[test] <- wert` \
  - `test`: Bedingung, die überprüft werden soll \
  - `wert`: Welcher Wert soll zugewiesen werden, wenn die Bedingung erfüllt ist?


``` r
personenDaten$altersgruppen[personenDaten$age <= 25] <- 1
personenDaten$altersgruppen[personenDaten$age > 25 
                            & personenDaten$age <= 30] <- 2
personenDaten$altersgruppen[personenDaten$age > 30 
                            & personenDaten$age <= 35] <- 3
personenDaten$altersgruppen[personenDaten$age > 35] <- 4
```
 
Überprüfung:


```
  original umkodiert
1       25         1
2       27         2
3       38         4
4       31         3
5       22         1
```

### Skalenbildung

**Situation:** aus den Werten der Items 1 bis 11 soll eine Summenscore/Mittelwertsscore gebildet werden.

`rowSums(x, na.rm = TRUE)` bildet zeilenweise die Summe
`rowMeans(x, na.rm = TRUE)` bildet zeilenweise den Mittelwert

  - `x`: Legt den Bereich des Datensatzes fest für den die Funktion durchgeführt werden soll. Hier
  sollen alle Zeilen und die Spalten von 2 bis 12 einbezigen werden. \
  - `na.rm`: Bei `TRUE` werden fehlende Werte bei der Berechnung ausgelassen, bei `FALSE` wird die
  Berechnung nicht durchgeführt, sobald ein einzelner Wert fehlt.


``` r
personenDaten$mittelwertsscore <- 
  rowMeans(personenDaten[,2:12], na.rm = T)
personenDaten$summenscore <- 
  rowSums(personenDaten[ ,2:12], na.rm = T)
```

### Arbeiten mit Daten (Datum)




``` r
class(personenDaten$Date)
[1] "character"
personenDaten$Date <- ymd(personenDaten$Date)
class(personenDaten$Date)
[1] "Date"
```

Damit können jetzt einzelne Jahre (Monate, Tage) von den Daten extrahiert werden:


``` r
year(personenDaten$Date[1])
[1] 2022
```

... oder auch Daten verglichen werden (früher oder später)


``` r
personenDaten$Date[1] < personenDaten$Date[2]
[1] TRUE
```

### Subsets erstellen

**Situation:** Es sollen alle Personen heraus gefiltert werden, deren score von Item 1 über 4 oder von Item 2 über 3 liegt. Der neue Datensatz soll nur die Variablen Person, Item 1 und Item 2 enthalten.

Die `subset(x, subset, select)` -Funktion bietet die Möglichkeit einen Datensatz nach bestimmten Voraussetzungen zu Filtern. \
  - `x`: Datensatz \
  - `subset`: logische Aussage, wenn `TRUE` wird der Eintrag in das Subset übernommen, wenn `FALSE`
  aussortiert. \
  - `select`: Variablen die im Subset beibehalten werden sollen.


``` r
personenDatenSubset <- subset(personenDaten, 
                              personenDaten$item1 > 4 |
                              personenDaten$item2 > 3, 
                              select = c(Person,item1,item2))
```

Überprüfung

```
   Person item1 item2
11     11     5     3
12     12     3     5
13     13     4     4
15     15     3     5
16     16     5     1
19     19     5     3
20     20     5     1
```

## Long- und Wide-Format

**Wide Format**

Eigenschaften:

- Eine Zeile pro Person
- Messwiederholungen (Items) als mehrere Variablen (Spalten) dargestellt
- Häufige Variante für Messwiederholungsdaten bei SPSS
- Praktisch bei der Dateneingabe


``` r
dataset <- read.table("data/Daten.txt", header = TRUE)
head(dataset, 5)
  Person item1 item2 item3 item4 item5 item6 item7 item8
1      1     3     2     3     2     2     1     4     1
2      2     4     1     4     3     1     1     4     1
3      3     3     1     3     3     1     1     3     1
4      4     2     2     2     2     2     2     2     2
5      5     2     2     2     2     1     1     3     2
  item9 item10 item11
1     1      3      4
2     1      4      2
3     1      3      4
4     2      2      4
5     2      2      2
```

**Long-Format**

Eigenschaften:
- Eine Zeile pro Bedingung (Item)
- Mehrere Zeilen pro Person
- Messwiederholung wird in mehreren Zeilen dargestellt


``` r
datasetLong <- read.table("data/Daten_Lang.txt", header = TRUE)
head(datasetLong, 14)
   Person  Item Score
1       1 item1     3
2       2 item1     4
3       3 item1     3
4       4 item1     2
5       5 item1     2
6       6 item1     4
7       7 item1     2
8       8 item1     3
9       9 item1     3
10     10 item1     4
11      1 item2     2
12      2 item2     1
13      3 item2     1
14      4 item2     2
```

**Wozu?**

Viele Modelle in R benötigen das Long Format.

###  Umwandlung Long- und Wide-Format

**Wide $\rightarrow$ Long**

Mit dem Paket `reshape2` können die Formate ineinander überführt werden.

Für die Umformung wird der Befehl `melt(data, id.vars = "...", variable.name = "...", value.name = "...")` verwendet \

- `data`: Datensatz, dessen Format geändert werden soll \
- `id.vars`: Variable(n), die als einzelne Variable beibehalten werden soll(en) und nicht
  gesplittet werden soll(en). Bennenung muss gleich wie im Ursprungsdatensatz sein! \
- `variable.name`: Bezeichnung der Spalte, in der Variablennamen aufgeführt werden sollen
  Achtung: nicht Variablennamen selbst auflisten! \
- `value.name`: Bezeichnung der Spalte, in der die Werte der zugehörigen Variablen aufgeführt
  werden sollen


``` r
library(reshape2)
dataset <- read.table("data/Daten.txt", header = TRUE)
datasetLong <- melt(dataset, id.vars = "Person",
                  variable.name = "item", value.name = "score")
head(datasetLong)
  Person  item score
1      1 item1     3
2      2 item1     4
3      3 item1     3
4      4 item1     2
5      5 item1     2
6      6 item1     4
```



**Long $\rightarrow$ Wide**

Ebenfalls aus dem Paket `reshape2` ermöglicht der Befehl `dcast(data, formula, value.var = "...")` das Transformieren vom Long ins Wide-Format \

- `data`: Datensatz, dessen Format geändert werden soll \
- `formula`: Welche Variablen sollen nach welcher Bedingung getrennt werden? \
  Format: `A ~ B` \
  - `A`: ID-Variablen (vgl. `id.vars`) \
  - `B`: Variable(n), die die Messungen (Zeitpunkte, Items, …) enthält $\rightarrow$ Bildet neue Spalten (vgl. `variable.name`) \
  - Mit `+` werden mehrere Variablen in `formula` kombiniert \
- `value.var`: Welche Variable im Long-Format enthält die Werte, die aufgesplittet werden sollen? (vgl. `value.name`) \


``` r
head(datasetLong,3)
  Person  item score
1      1 item1     3
2      2 item1     4
3      3 item1     3
```




``` r
datasetWide <- dcast(datasetLong, Person ~ item, value.var = "score")
head(datasetWide)
  Person item1 item2 item3 item4 item5 item6 item7 item8
1      1     3     2     3     2     2     1     4     1
2      2     4     1     4     3     1     1     4     1
3      3     3     1     3     3     1     1     3     1
4      4     2     2     2     2     2     2     2     2
5      5     2     2     2     2     1     1     3     2
6      6     4     1     3     3     1     1     4     1
  item9 item10 item11
1     1      3      4
2     1      4      2
3     1      3      4
4     2      2      4
5     2      2      2
6     1      4      5
```

