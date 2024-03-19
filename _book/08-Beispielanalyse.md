# Beispielanalyse

## Forschungsfrage

Sind Menschen glücklicher, die sich gesund
verhalten?

## Datensatz

ESS7 - integrated file, edition 2.2:   
- [European Social Survey (2014)](https://ess-search.nsd.no/en/study/ccd56840-e949-4320-945a-927c49e1dc4f)

## Variablen

**AV (Abhängige Variablen)** 

- happiness: happy (num; 0-10) 0 = extremely unhappy, 10 = extremely happy 
  - "Taking all things together, how happy would you say you are?"

**UV (Unabhängige Variablen)** 

- Eat Fruit: etfruit (cat) 
- Eat Veg: eatveg (cat) 
- Sport: dosprt (num) 
- cigarettes: cgtsday (num) 
- how often alkohol: alcfreq (cat)

**Kontrollvariablen**

-   gender: gndr (cat)
-   age: agea (num)
-   income: hinctnta (cat)
-   country: cntry (chr)

## Analyse

**Vorgehen**
  - Daten beschaffen
  - Daten einlesen 
  - Daten säubern
  - Index bilden
  - Daten sichten
  - deskriptive Statistiken
  - Regressionsmodelle

**Daten beschaffen und einlesen**

Die Daten sind für alle über die Webseite der ESS zugänglich. Um umständliches Anmelden zu vermeiden könnt ihr sie über den Moodle-Kurs runterladen.

Die genaue Beschreibung der Variablen könnt ihr ebenfalls über die Webseite einsehen.

**Datensatz säubern**

Für unsere Analyse möchten wir nur Variablen im Datensatz behalten, die wir tatsächlich für unsere Analyse brauchen (= AV, UV und KV), und alle anderen verwerfen.

Diese Variablen haben teilweise Ausprägungen, die für unsere Analyse nicht zielführend sind. Fälle mit diesen Ausprägungen möchten wir ebenfalls ausschließen. (Antworten: "Refusal","Don't Know","No answer"; bei cgtsday möchten wir "not applicable" beibehalten)

**Indexbildung**

Nach dem einlesen der Daten bauen wir einen Index, welcher sich aus negativen Werten für gesundheitsschädigendes Verhalten und positiven Werten für gesundheitsförderndes Verhalten aufwiegt. Gesundheitsschädigendes Verhalten ist dabei doppelt so stark gewichtet wie förderndes. Die Einstufung der einzelnen Variablen liegt dabei kein validiertes Wissen zugrunde, was der Demonstration im Weiteren aber nicht schaden sollte.

**Deskriptive Statistik**

Zunächst möchten wir einen Überblick über die Daten erhalten und lassen uns die Verteilungen für Zufriedenheit und unseren Index ausgeben. Zudem wollen wir mittels eines t-Tests überprüfen, ob geschlechtsspezifische Unterschiede bezüglich der Zufriedenheit statistisch signifikant sind.  

**Korrelation**

Zu guter Letzt möchten wir die Korrelation zwischen Zufriedenheit und dem Index berechnen, um im Anschluss zwei lineare Regressionsmodelle zu definieren und schätzen zu lassen. Eines davon mit und eines davon ohne Kontrollvariablen.

## Einzelne Schritte

**Daten einlesen**

1. Lese den Datensatz ein.

**Datensatz säubern**
1. Erstelle einen neuen Datensatz mit den für die Analyse relevante Variablen (AV, UV und KV).
2. Lösche alle Fälle die **NA**-Werte enthalten. (Hinweis: **complete.cases()** prüft ob **NA** vorhanden sind)
3. Lösche alle Fälle die keine Informationen enthalten ("Refusal","Don't Know","No answer").

**Indexbildung**  

Erstelle eine neue Variable (health_index) nach den folgenden Kriterien:   
- Isst eine Person weniger als 4 Mal, aber mindestens ein Mal die Woche Obst oder seltener werten wir das als ungesund (-1). Isst sie häufiger Obst werten wir das als gesund (0.5).  
- Isst eine Person weniger als 4 Mal, aber mindestens ein Mal die Woche Gemüse oder seltener werten wir das als ungesund (-1). Isst sie häufiger Gemüse werten wir das als gesund (0.5).  
- Macht eine Person weniger als 3 Mal die Woche Sport werten wir das als ungesund (-1). Macht sie häufiger Sport als gesund (0.5).
- Raucht eine Person auch nur eine Zigarette am Tag werten wir das als ungesund (-1). Raucht sie gar nicht ("not applicable") oder 0 Zigaretten am Tag werten wir das als einflusslos (0).  
- Trinkt eine Person ein Mal die Woche oder häufiger Alkohol werten wir das als ungesund (-1), trinkt sie seltener als einflusslos (0).

**Daten sichten**

1. Wie ist der Index (*health_index*) verteilt? Erstelle einen Plot um dir die Verteilung anzuzeigen.  
2. Wie ist die Zufriedenheit (*happy*) verteilt? Erstelle einen Plot.
3. Wie ist die durchschnittliche Zufriedenheit nach Geschlecht verteilt?  
4. Ist der Unterschied in der Zufriedenheit nach Geschlecht statistisch signifikant? (Hinweis: Wie funktioniert die Funktion **t.test()**?)

**Korrelation**

1. Erstelle einen Boxplot mit den Variablen *happy* und *health_index* um einen ersten Überblick über deren Korrelation zu bekommen. 
2. Berechne den Korrelationskoeffizienten nach Pearson.

**Regression**

| 1. Berechne folgende Regressionsmodelle und lass dir jeweils die Konfidenzintervalle der Regressionskoeffizienten ausgeben:
|  1.1 Univariat: Modell 1 = happy ~ health_index
|  1.2 Multivariat: Modell 2 = happy ~ health_index + gndr + age + inc 
|      (Achtung: gndr: gndr faktorisiert (**factor()**), age: agea standardisiert 
|      (**scale()**), inc: hinctnta gruppiert (**ggplot2::cut\_number()**)
| 2. Erstelle eine .html-Datei mit einer gemeinsamen Regressionstabelle für Modell 1 und Modell 2.
