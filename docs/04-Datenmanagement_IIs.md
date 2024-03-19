# Datenmanagement II

## Datenbearbeitung

- Wir wissen nun wie Daten eingelesen und zusammengefügt werden
- Bevor wir mit unserem Datensatz Statistik betreiben können, müssen aber meist noch weitere Schritte der Datenbearbeitung durchgeführt werden
- Beispieldatensatz:


```r
PersonenDaten <- read.table("data/PersonenDaten.txt", header = TRUE)
head(PersonenDaten, 5)
  Person item1 item2 item3 item4 item5 item6 item7 item8
1      1     3     2     3     2     2     1     4     1
2      2     4     1     4     3     1     1     4     1
3      3     3     1     3     3     1     1     3     1
4      4     2     2     2     2     2     2     2     2
5      5     2     2     2     2     1     1     3     2
  item9 item10 item11 age sex
1     1      3      4  25   2
2     1      4      2  27   1
3     1      3      4  38   1
4     2      2      4  31   2
5     2      2      2  22   1
```

### Umkodierung

**Situation:** Im PersonDaten- Datensatz ist die Variable \textit{sex} mit 1 und 2 Kodiert. Die Kodierung soll aber 0 und 1 sein.
 
Der *$*-Operator schafft hier Abhilfe wenn:

- eine neue Variable aus einer alten erstellt werden soll


```r
PersonenDaten$sexUmkodiert <- PersonenDaten$sex-1
PersonenDaten$sexUmkodiert
 [1] 1 0 0 1 0 1 1 1 0 0 1 1 1 1 1 0 0 1 0 1
```

- eine existierende Variable überschrieben werden soll


```r
PersonenDaten$sex <- PersonenDaten$sex-1
PersonenDaten$sex
 [1] 1 0 0 1 0 1 1 1 0 0 1 1 1 1 1 0 0 1 0 1
```

**Situation:** Einige Items im Datensatz sind umgepolt worden. Für die weitere Verarbeitung sollen sie zurückgepolt werden.

- Anwendung der Formel:  Item_Umkodiert = max(Skala_Item)+ 1 - Item_Original

- Beispiel: Umpolung von Item3 mit 6-stufiger Skala


```r
PersonenDaten$item3_umkodiert <- 6 + 1 - PersonenDaten$item3
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

**ifelse(test, yes, no)** liefert die Möglichkeit, R-Befehle abhängig von einer Bedingung auszuführen:<br>   
- **test**: Bedingung, die überprüft werden soll<br>
- **yes**: Welcher Wert soll zugewiesen werden, wenn die<br> Bedingung erfüllt ist?
- **no**: Welcher Wert soll zugewiesen werden, wenn die<br> Bedingung nicht erfüllt ist?


```r
PersonenDaten$AlterDichotom <- ifelse(PersonenDaten$age <= 30, 0, 1)
```

Überprüfung:


```r
Überprüfung <- data.frame(Original=PersonenDaten$age,
                          Umkodiert=PersonenDaten$AlterDichotom)
head(Überprüfung,5)
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

**Dataset$neueVariable[test] <- yes**: Logisches Indizieren ist ein einfacher Weg, Variablenwerte abhängig von Bedingungen festzusetzen

- **test**: Bedingung, die überprüft werden soll
- **yes**: Welcher Wert soll zugewiesen werden, wenn die Bedingung erfüllt ist?


```r
PersonenDaten$Altersgruppen[PersonenDaten$age <= 25] <- 1
PersonenDaten$Altersgruppen[PersonenDaten$age > 25 
                            & PersonenDaten$age <= 30] <- 2
PersonenDaten$Altersgruppen[PersonenDaten$age > 30 
                            & PersonenDaten$age <= 35] <- 3
PersonenDaten$Altersgruppen[PersonenDaten$age > 35] <- 4
```
 
Überprüfung:


```
  Original Umkodiert
1       25         1
2       27         2
3       38         4
4       31         3
5       22         1
```

### Skalenbildung

**Situation:** aus den Werten der Items 1 bis 11 soll eine Summenscore/Mittelwertsscore gebildet werden.

**rowSums(x, na.rm = TRUE)** bildet zeilenweise die Summe

**rowMeans(x, na.rm = TRUE)** bildet zeilenweise den Mittelwert

- **x**: Legt den Bereich des Datensatzes fest für den die Funktion durchgeführt werden soll. Hier sollen alle Zeilen und die Spalten von 2 bis 12 einbezigen werden.
- **na.rm**: Bei **TRUE** werden fehlende Werte bei der Berechnung ausgelassen, bei **FALSE** wird die Berechnung nicht durchgeführt, sobald ein einzelner Wert fehlt


```r
PersonenDaten$Mittelwertsscore <- 
  rowMeans(PersonenDaten[,2:12], na.rm = T)
PersonenDaten$Summenscore <- 
  rowSums(PersonenDaten[ ,2:12], na.rm = T)
```

### Subsets erstellen

**Situation:** Es sollen alle Personen heraus gefiltert werden, deren score von Item 1 über 4 oder von Item 2 über 3 liegt. Der neue Datensatz soll nur die Variablen Person, Item 1 und Item 2 enthalten.

Die **subset(x, subset, select)** -Funktion bietet die Möglichkeit einen Datensatz nach bestimmten Voraussetzungen zu Filtern

- **x**: Datensatz<br>
- **subset**: logische Aussage, wenn **TRUE** wird der Eintrag in das Subset übernommen, wenn **FALSE** aussortiert.<br>
- **select**: Variablen die im Subset beibehalten werden sollen


```r
PersonenDatenSubset <- subset(PersonenDaten, 
                              PersonenDaten$item1 > 4|
                              PersonenDaten$item2 > 3, 
                              select =
                              c(Person,item1,item2))
PersonenDatenSubset
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

### Wide Format

Alle Datensätz mit denen wir bisher gearbeitet haben, hatten folgende Eigenschaften:   
- Eine Zeile pro Person
- Messwiederholungen (Items) als mehrere Variablen (Spalten) dargestellt
- Häufige Variante für Messwiederholungsdaten bei SPSS
- Praktisch bei der Dateneingabe


```r
Dataset <- read.table("data/Daten.txt",header = TRUE)
head(Dataset, 5)
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

### Long-Format


```r
DatasetLong <- read.table("data/Daten_Lang.txt", header = TRUE)
head(DatasetLong,14)
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

**Eigenschaften:**
- Eine Zeile pro Bedingung (Item)
- Mehrere Zeilen pro Person
- Messwiederholung wird in mehreren Zeilen dargestellt

**Wozu?**

Viele Modelle in R benötigen das Long Format 

###  Long- und Wide-Format

**Wide --> Long**

Mit dem Paket **reshape2** können die Formate ineinander überführt werden.

Für die Umformung wird der Befehl **melt(data, id.vars = "...", variable.name = "...", value.name = "...")** verwendet:   
- **data**: Datensatz, dessen Format geändert werden soll<br>
- **id.vars**: Variable(n), die als einzelne Variable beibehalten werden soll(en) und nicht gesplittet werden soll(en). Bennenung muss gleich wie im Ursprungsdatensatz sein!<br>
- **variable.name**: Bezeichnung der Spalte, in der Variablennamen aufgeführt werden sollen  Achtung: nicht Variablennamen selbst auflisten!<br>
- **value.name**: Bezeichnung der Spalte, in der die Werte der zugehörigen Variablen aufgeführt werden sollen


```r
library(reshape2)
Dataset <- read.table("data/Daten.txt", header = TRUE)
DatenLong <- melt(Dataset, id.vars = "Person",
                  variable.name = "item", value.name = "score")
head(DatenLong)
  Person  item score
1      1 item1     3
2      2 item1     4
3      3 item1     3
4      4 item1     2
5      5 item1     2
6      6 item1     4
```

- Mit dem Paket reshape2 können die Formate ineinander übergeführt werden
- Der Befehl dafür **dcast(data, formula, value.var = "...")**
  - **data**: Datensatz, dessen Format geändert werden soll<br>
  - **formula**: Welche Variablen sollen nach welcher Bedingung getrennt werden? Format: **A ~ B}**<br>
    - **A**: ID-Variablen (vgl. **id.vars**)<br>
    - **B**: Variable(n), die die Messungen (Zeitpunkte, Items, …) enthält --> Bildet neue Spalten (vgl. **variable.name**)<br>
    - Mit **+** werden mehrere Variablen in **formula** kombiniert<br>
  - **value.var**: Welche Variable im Long-Format enthält die Werte, die auf gesplittet werden sollen? (vgl. **value.name**)


```r
head(DatenLong,3)
  Person  item score
1      1 item1     3
2      2 item1     4
3      3 item1     3
```


```r
library(reshape2)
DatenWide <- dcast(DatenLong, Person ~ item, value.var = "score")
head(DatenWide,5)
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

