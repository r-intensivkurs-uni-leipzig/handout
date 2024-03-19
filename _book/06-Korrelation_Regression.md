# Korrelation und Regression 

## Kovarianz und Korrelation

**Beispieldatasatz**

Wie hängen die Itemantworten zusammen?


```r
# Beispieldatasatz einlesen
df_exam <- read.table("data/Daten.txt", header = TRUE)

# Ersten 5 Zeilen und 8 Spalten anzeigen
head(df_exam, c(5,8))
  Person item1 item2 item3 item4 item5 item6 item7
1      1     3     2     3     2     2     1     4
2      2     4     1     4     3     1     1     4
3      3     3     1     3     3     1     1     3
4      4     2     2     2     2     2     2     2
5      5     2     2     2     2     1     1     3
```

**Zwei Variablen**

- Für die Berechnung der Korrelation wird **cor()** verwendet, für die Berechnung der Kovarianz **cov()**    
- Argumente bei **cor(x, y, method = "…", use = "…")**
  - **x** und **y**: datavektoren  
  - **method**: Welche Korrelation soll berechnet werden?   
    - Produkt-Moment-Korrelation (**"pearson"**), Rangkorrelation &rho; (**"spearman"**) oder Rangkorrelation &tau; (**"kendall"**)
  - **use**: Umgang mit fehlenden Werten (siehe nächste Folie)

**Fehlende Werte**

- **Use = "…"** regelt den Umgang der Korrelationsfunktionen mit fehlenden Werten
  - **"everything"**: Kein Umgang mit fehlenden Werten &rarr; bei einzelnen fehlenden Werten wird keine (einzige) Korrelation berechnet                (Ergebnis: **NA**) 
  - **"pairwise"**: Korrelationen werden jeweils mit den vollständigen Fällen pro Variablenpaar berechnet (paarweiser Ausschluss)
  - **"complete"**: Korrelationen werden nur aus in allen Variablen vollständigen Fällen berechnet (fallweiser Ausschluss) 

everything|  pairwise  |  complete 
----------|------------|--------------
![](Abbildungen/everything.png){height=150px} | ![](Abbildungen/pairwise.png){height=150px}| ![](Abbildungen/complete.png){height=150px}

**Funktion cor.test()**

- **cor.test(x, y, method = "…", use = "…", alternative = "…", conf.level =     …, …)** für **Inferenzstatistik** bei **Korrelationen**.
  - **x, y, method, use**: Siehe **cor()**
  - **alternative**: Ist die Testrichtung… 
    - Ungerichtet (default): **"two.sided"**
    - einseitig: Positive Korrelationshypothese **"greater"**
    - einseitig: Negative Korrelationshypothese **"less"**
  - **conf.level**: Konfidenzniveau (1 - &alpha;)
    - **default**: 0.95
	
**Beispiel**


```r
# Korrelation item1, item2
cor(df_exam$item1, df_exam$item2, method = "pearson")
[1] -0.7698004

# Produkt-Moment-Korrelation, Hypothese: negativ ,alpha = 1%
cor.test(df_exam$item1, df_exam$item2, method = "pearson",
         alternative = "less", conf.level = .99)

	Pearson's product-moment correlation

data:  df_exam$item1 and df_exam$item2
t = -3.4112, df = 8, p-value = 0.004603
alternative hypothesis: true correlation is less than 0
99 percent confidence interval:
 -1.0000000 -0.1396423
sample estimates:
       cor 
-0.7698004 
```

## Matrizen

Werden Dataframes (oder Teile dessen) in **cov()** oder **cor()** eingefügt, werden Kovarianz- und Korrelationsmatrizen ausgegeben:


```r
cov(df_exam[, 2:4])
           item1      item2      item3
item1  0.6666667 -0.4444444  0.3333333
item2 -0.4444444  0.5000000 -0.2222222
item3  0.3333333 -0.2222222  0.4444444

cor(df_exam[, c("item1", "item2", "item3")], method = "pearson")
           item1      item2      item3
item1  1.0000000 -0.7698004  0.6123724
item2 -0.7698004  1.0000000 -0.4714045
item3  0.6123724 -0.4714045  1.0000000
```

- **corr.test()** (Paket **psych**) liefert inferenzstatistische Berechnungen auch  für Korrelationsmatrizen
- **corr.test(df, method = "…", adjust = "…", alpha = "…")**
  - **df, method**: Siehe **cor()**, Kendall &tau; kann aber nicht verwendet werden
  - **adjust**: Methode zur &alpha;-Fehler-Adjustierung
    - **"none", "bonferroni", … **
    - Für weitere siehe **?p.adjust** und **?corr.test**
  - **alpha**: Signifikanzniveau &alpha;     
    

```r
library(psych)
corr.test(df_exam[ ,2:4], method = "pearson", adjust = "bonferroni")
Call:corr.test(x = df_exam[, 2:4], method = "pearson", adjust = "bonferroni")
Correlation matrix 
      item1 item2 item3
item1  1.00 -0.77  0.61
item2 -0.77  1.00 -0.47
item3  0.61 -0.47  1.00
Sample Size 
[1] 10
Probability values (Entries above the diagonal are adjusted for multiple tests.) 
      item1 item2 item3
item1  0.00  0.03  0.18
item2  0.01  0.00  0.51
item3  0.06  0.17  0.00

 To see confidence intervals of the correlations, print with the short=FALSE option
```

**Extraktion von Werten**

- Mit $ können Werte aus der Berechnung extrahiert werden
- Dafür Korrelationsmatrix als Objekt speichern
  - $se für Extraktion der Standardfehler
  - $ci für Extraktion der Konfidenzintervalle
  - $p für Extraktion der p-Werte
  - $t für Extraktion der t-Werte

**Beispiel**


```r
# Definition eines Objekts mit Inferenzstatistik für df_exam
KorMat <- corr.test(df_exam[ ,2:4], method = "pearson", adjust = "bonferroni")
# Ausgabe Standardfehler
KorMat$se
          item1     item2     item3
item1 0.0000000 0.2256677 0.2795085
item2 0.2256677 0.0000000 0.3118048
item3 0.2795085 0.3118048 0.0000000
# Ausgabe Konfidenzintervalle
KorMat$ci
                 lower          r      upper          p
item1-item2 -0.9425738 -0.7698004 -0.2720171 0.00920665
item1-item3 -0.0280809  0.6123724  0.8963838 0.05983788
item2-item3 -0.8490310 -0.4714045  0.2250053 0.16902020
# Ausgabe p-Werte
KorMat$p
           item1      item2     item3
item1 0.00000000 0.02761995 0.1795136
item2 0.00920665 0.00000000 0.5070606
item3 0.05983788 0.16902020 0.0000000
```

## (Multiple) lineare Regression

**Wiederholung:**

- Ziel: Ein Kriterium (abhängige Variable) durch die Kombination von Prädiktoren (unabhängige Variablen) vorhersagen

- Signifikanztests für
    - einzelne Prädiktoren
    - das Gesamtmodell
    - Modellvergleiche

### Modelldefinition

- Um eine Regression in R zu rechnen, muss man anhand der vorliegenden Variablen ein Regressionsmodell spezifizieren
- Regressionsmodelle werden in sogenannten Formel-Objekten definiert
  - Struktur: Abhängige Variable(n) ~ Unabhängige Variable + Unabhängige Variable + …
  - Mehrere Variablen werden mit + verbunden
- Variablenbezeichnungen entsprechend anpassen
  - item1 soll durch item2 und item3 vorhergesagt werden



```r
# Beispiel Modelldefinition
model1 <- item1 ~ item2 + item3
```
	
### Modellschätzung

- Anhand des definierten Regressionsmodells und der vorhandenen Daten kann R nun die Modellparameter (inkl. Modellpassung) schätzen
- Lineare Regressionsmodelle werden mit **lm(formula = …, data = …)** geschätzt:
  - **formula**: Definiert das Modell
  - **data**: dataframe, der die Variablen enthält
- **lm()** sollte für die weiteren Berechnungen als Objekt gesichert werden

**Beispiel Modellschätzung**


```r
# Modelldefinition
model1 <- item1 ~ item2 + item3

# Modellschätzung und Sicherung in fit1
fit1 <- lm(formula = model1, data = df_exam)

# Alternativ
fit1 <- lm(formula = item1 ~ item2 + item3, data = df_exam)

# Modell anzeigen
fit1

Call:
lm(formula = item1 ~ item2 + item3, data = df_exam)

Coefficients:
(Intercept)        item2        item3  
     2.8929      -0.7143       0.3929  
```

- Mit **summary()** lassen sich detaillierte Informationen zum Regressionsmodell ausgeben:



```r
# Ausgabe Informationen Regressionsmodell, fit1
summary(fit1)

Call:
lm(formula = item1 ~ item2 + item3, data = df_exam)

Residuals:
     Min       1Q   Median       3Q      Max 
-0.75000 -0.33036 -0.08929  0.33036  0.64286 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)  
(Intercept)   2.8929     1.1752   2.462   0.0434 *
item2        -0.7143     0.2832  -2.523   0.0397 *
item3         0.3929     0.3003   1.308   0.2322  
---
Signif. codes:  
0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.5297 on 7 degrees of freedom
Multiple R-squared:  0.6726,	Adjusted R-squared:  0.5791 
F-statistic: 7.191 on 2 and 7 DF,  p-value: 0.02008
```


### Modellvergleich

- Zwei genestete Modelle können mit **anova(fit1, fit2)** miteinander verglichen werden
  - **fit1**: Ausgangsmodell (restringiertes Modell)
  - **fit2**: Erweitertes Modell (mit zusätzlichen Prädiktoren)


```r
# Modelldefinition, model1 + model2
model1 <- item1 ~ item2 + item3
model2 <- item1 ~ item2 + item3 + item4

# Modellschätzung, fit1 + fit2
fit1 <- lm(model1, df_exam)
fit2 <- lm(model2, df_exam)
```

**Beispiel Modellvergleich**


```r
# Modellvergleich, fit1 + fit2
anova(fit1, fit2)
Analysis of Variance Table

Model 1: item1 ~ item2 + item3
Model 2: item1 ~ item2 + item3 + item4
  Res.Df     RSS Df Sum of Sq      F  Pr(>F)  
1      7 1.96429                              
2      6 0.80569  1    1.1586 8.6282 0.02604 *
---
Signif. codes:  
0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

## Ergänzungen

### Matrizen

- Kovarianzmatrizen werden mit **cov2cor()** in Korrelationsmatrizen umgewandelt:


```r
# Zuweisung
Kova <- cov(df_exam[ , 2:4])
# Berechnung
cov2cor(Kova)
           item1      item2      item3
item1  1.0000000 -0.7698004  0.6123724
item2 -0.7698004  1.0000000 -0.4714045
item3  0.6123724 -0.4714045  1.0000000
# Alternativ
cor(df_exam[ , 2:4])
           item1      item2      item3
item1  1.0000000 -0.7698004  0.6123724
item2 -0.7698004  1.0000000 -0.4714045
item3  0.6123724 -0.4714045  1.0000000
```

### Weitere Korrelationen

- Mit dem Paket **psych** können zudem
  - Partialkorrelationen (**partial.r()**),
  - tetrachorische (**tetrachoric()**),
  - polychorische (**polychoric()**),
  - biserale (**biserial()**) 
  - und polyseriale (**polyserial()**) Korrelationen berechnet werden
- Mit dem Paket **ppcor** können Semipartialkorrelationen berechnet werden

### Standartisierte und unstandartisierte Regressionkoeffizienten

- Mit **coefficients()** oder **$coefficients** lassen sich die nicht-standardisierten Regressionskoeffizienten ausgeben
- Mit **lm.beta()** aus dem Paket **QuantPsyc** lassen sich standardisierte Regressionskoeffizienten ausgeben



```r
# Unstandartisierte Regressionskoeffizienten
coefficients(fit1)
(Intercept)       item2       item3 
  2.8928571  -0.7142857   0.3928571 

# Ausgabe
fit1$coefficients
(Intercept)       item2       item3 
  2.8928571  -0.7142857   0.3928571 

# Standartisierte Regressionskoeffizienten
QuantPsyc::lm.beta(fit1)
     item2      item3 
-0.6185896  0.3207665 
```

### Vorhergesagte Werte

- **predict()**, **fitted.values()** oder **\$fitted.values** gibt die vorhergesagten Werte für jeden Datenpunkt als Vektor aus:



```r
# Vorhersage Werte, fit1
predict(fit1)
       1        2        3        4        5        6 
2.642857 3.750000 3.357143 2.250000 2.250000 3.357143 
       7        8        9       10 
1.928571 3.750000 3.357143 3.357143 

# Alternative 1
fitted.values(fit1)
       1        2        3        4        5        6 
2.642857 3.750000 3.357143 2.250000 2.250000 3.357143 
       7        8        9       10 
1.928571 3.750000 3.357143 3.357143 

# Alternative 2
fit1$fitted.values
       1        2        3        4        5        6 
2.642857 3.750000 3.357143 2.250000 2.250000 3.357143 
       7        8        9       10 
1.928571 3.750000 3.357143 3.357143 
```

### Residuen

- **resid()**, **residuals()** oder **\$residuals** gibt die unstandardisierten Residuen pro datapunkt aus
- **rstandard()** gibt die standardisierten Residuen aus


```r
# Residuen, fit1
fit1$residuals
          1           2           3           4           5 
 0.35714286  0.25000000 -0.35714286 -0.25000000 -0.25000000 
          6           7           8           9          10 
 0.64285714  0.07142857 -0.75000000 -0.35714286  0.64285714 
```

### Modelldefinition

- Spezielle Modelle:
  - Modell ohne Prädiktoren (Nullmodell): AV ~ 1
  - Modell ohne Intercept (Konstante): AV ~ 0 + UV1 + …
  - Interaktionsterme (Moderation):
    - AV ~ UV1 + UV2 + UV1*UV2
    - Statt "$*$" kann auch "$:$" verwendet werden


### Hinweise zu weiteren Regressionsmodellen in R

- Literatur:
  - Fox, J., & Weisberg, S. (2011). An R companion to applied regression.     London: SAGE. 
  - James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). An introduction to statistical learning: With applications in R. Springer     texts in statistics. New York: Springer.
- In R sind viele weitere Verfahren zur Modellselektion, -schätzung und -bewertung umgesetzt wie etwa:
  - Bootstrapping
  - Kreuzvalidierung
  - Shrinkage (Lasse/Ridge) Regression
  - Robust Regression
  - Power Analyse

### General Linear Model (GLM)

**glm(forumla, family, data)**

- Generalisierte lineare Modelle als Verallgemeinerung des linearen Modells $\rightarrow$ Breite Einsatzmöglichkeiten
  - **formula**: Wie bei **lm()**
  - **data**: Wie bei **lm()**
  - **family:** Definiert die Zufallsverteilung von Y und die Linkfunktion
- Beispiele:
  - GLM mit Normalverteilung und ohne besondere Linkfunktion (**familiy = gaussian(link = "identity")**)
    $\rightarrow$ Entspricht **lm()**
  - GLM mit Binomialverteilung und logistischer Linkfunktion (**family = binomial(link = "logit")**)
       $\rightarrow$ Binär-logistische Regression

### Mediation und SEM

- Paket **lavaan**
  - Ständig weiterentwickeltes Paket zur Schätzung von Pfad- und              Strukturgleichungsmodellen in R
  - Ermöglicht auch Bootstrapping-Konfidenzintervalle für indirekte           Effekte
  - Ausführliche Dokumentation mit Beispielcodes: 
    - http://lavaan.ugent.be/tutorial/est.html

### Linear Mixed Model (LMM)

- Paket **lme4**
  - Liefert eine breite und gut erprobte Palette an Werkzeugen für            linear mixed models (Hierarchische lineare Modelle; Mehrebenenmodelle)
  - Siehe: Bates, D., Mächler, M., Bolker, B., & Walker, S. (2015).           Fitting Linear Mixed-Effects Models Using lme4. Journal of Statistical     Software, 67(1).
- Paket **lmerTest**
  - Reicht Signifikanztests nach, die in lme4 standardmäßig nicht implementiert sind
  - https://cran.r-project.org/web/packages/lmerTest/index.html
