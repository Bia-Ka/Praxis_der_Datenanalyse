



# Das Einmaleins der Datenaufbereitung





\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">Lernziele:

- Typische Probleme der Datenaufbereitung kennen.
- Typische Probleme der Datenaufbereitung bearbeiten können.
</div>\EndKnitrBlock{rmdcaution}


Laden wir zuerst die benögtigten Pakete; v.a. ist das `dplyr` and friends. Das geht mit dem Paket `tidyverse`. 

```r
library(tidyverse)
library(corrr)
library(gridExtra)
library(car)
```


Stellen wir einige typische Probleme des Datenjudo (genauer: der Datenaufbereitung) zusammen. Probleme heißt hier nicht, dass es etwas Schlimmes passiert ist, sondern es ist gemeint, wir schauen uns ein paar typische Aufgabenstellungen an, die im Rahmen der Datenaufbereitung häufig anfallen. 


## Datenaufbereitung


### Auf fehlende Werte prüfen 
Das geht recht einfach mit `summarise(mein_dataframe)`. Der Befehl liefert für jede Spalte des Dataframe `mein_dataframe` die Anzahl der fehlenden Werte zurück.


```r
wo_men <- read.csv("https://sebastiansauer.github.io/data/wo_men.csv")
glimpse(wo_men)
#> Observations: 101
#> Variables: 4
#> $ time      <fctr> 04.10.2016 17:58:51, 04.10.2016 17:58:59, 04.10.201...
#> $ sex       <fctr> woman, woman, woman, woman, man, woman, woman, woma...
#> $ height    <dbl> 160, 171, 174, 176, 195, 157, 160, 178, 168, 171, 16...
#> $ shoe_size <dbl> 40, 39, 39, 40, 46, 37, 38, 39, 38, 41, 39, 44, 38, ...
```

### Fälle mit fehlenden Werte löschen
Weist eine Variable (Spalte) "wenig" fehlende Werte auf, so kann es schlau sein, nichts zu tun. Eine andere Möglichkeit besteht darin, alle entsprechenden Zeilen zu löschen. Man sollte aber schauen, wie viele Zeilen dadurch verloren gehen.


```r
nrow(wo_men)
#> [1] 101
wo_men %>% 
  na.omit %>% 
  nrow
#> [1] 100
```


\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">Lernziele:

Bei mit der Pfeife verketteten Befehlen darf man für Funktionen die runden Klammern weglassen, wenn man keinen Parameter schreibt. Also `nrow` ist erlaubt bei `dplyr`, wo es eigentlich `nrow()` heißen müsste. Sie dürfen die Klammern natürlich schreiben, aber sie müssen nicht.
</div>\EndKnitrBlock{rmdcaution}




Hier verlieren wir nur 1 Zeile, das verschmerzen wir. Welche eigentlich?

```r
wo_men %>% 
  rownames_to_column %>%  # Zeilennummer werden eine eigene Spalte
  filter(!complete.cases(.))  # Nur die nicht-kompletten Fälle filtern
#>   rowname                time  sex height shoe_size
#> 1      86 11.10.2016 12:44:06 <NA>     NA        NA
```

Man beachte, dass der Punkt `.` für den Datensatz steht, wie er vom letzten Schritt weitergegeben wurde. Innerhalb einer dplyr-Befehls-Kette können wir den Datensatz, wie er im letzten Schritt beschaffen war, stets mit `.` ansprechen; ganz praktisch, weil schnell zu tippen. Natürlich könnten wir diesen Datensatz jetzt als neues Objekt speichern und damit weiter arbeiten. Das Ausrufezeichen `!` steht für logisches "Nicht".

In Pseudo-Syntax liest es sich so:

\BeginKnitrBlock{rmdpseudocode}<div class="rmdpseudocode">Nehme den Datensatz `wo_men` UND DANN...  
Mache aus den Zeilennamen (hier identisch zu Zeilennummer) eine eigene Spalte UND DANN...  
filtere die nicht-kompletten Fälle 
</div>\EndKnitrBlock{rmdpseudocode}


### Fehlende Werte ggf. ersetzen  
Ist die Anzahl der fehlenden Werte zu groß, als dass wir es verkraften könnten, die Zeilen zu löschen, so können wir die fehlenden Werte ersetzen. Allein, das ist ein weites Feld und übersteigt den Anspruch dieses Kurses^[Das sagen Autoren, wenn sie nicht genau wissen, wie etwas funktioniert.]. Eine einfache, aber nicht die beste Möglichkeit, besteht darin, die fehlenden Werte durch einen repräsentativen Wert, z.B. den Mittelwert der Spalte, zu ersetzen.


```r
wo_men$height <- replace(wo_men$height, is.na(wo_men$height), mean(wo_men$height, na.rm = TRUE))
  
```

`replace`^[aus dem "Standard-R", d.h. Paket "base".] ersetzt Werte aus dem Vektor `wo_men$height` alle Werte, für die `is.na(wo_men$height)` wahr ist. Diese Werte werden durch den Mittelwert der Spalte ersetzt^[Hier findet sich eine ausführlichere Darstellung: https://sebastiansauer.github.io/checklist_data_cleansing/index.html].



### Nach Fehlern suchen
Leicht schleichen sich Tippfehler oder andere Fehler ein. Man sollte darauf prüfen; so könnte man sich ein Histogramm ausgeben lassen pro Variable, um "ungewöhnliche" Werte gut zu erkennen. Meist geht das besser als durch das reine Betrachten von Zahlen. Gibt es wenig unterschiedliche Werte, so kann man sich auch die unterschiedlichen Werte ausgeben lassen.


```r
wo_men %>% 
  count(shoe_size) %>% 
  head  # nur die ersten paar Zeilen
#> # A tibble: 6 × 2
#>   shoe_size     n
#>       <dbl> <int>
#> 1      35.0     1
#> 2      36.0     6
#> 3      36.5     1
#> 4      37.0    14
#> 5      38.0    26
#> 6      39.0    18
```


### Ausreiser identifizieren
Ähnlich zu Fehlern, steht man Ausreisern häufig skeptisch gegenüber. Allerdings kann man nicht pauschal sagen, das Extremwerte entfernt werden sollen: Vielleicht war jemand in der Stichprobe wirklich nur 1.20m groß? Hier gilt es, begründet und nachvollziehbar im Einzelfall zu entscheiden. Histogramme und Boxplots sind wieder ein geeignetes Mittel, um Ausreiser zu finden.

<img src="043_Typische_Probleme_Datenanalyse_files/figure-html/unnamed-chunk-8-1.png" width="70%" style="display: block; margin: auto;" />


### Hochkorrelierte Variablen finden
Haben zwei Leute die gleiche Meinung, so ist einer von beiden überflüssig - wird behauptet. Ähnlich bei Variablen; sind zwei Variablen sehr hoch korreliert (>.9, als grober (!) Richtwert), so bringt die zweite kaum Informationszuwachs zur ersten. Und kann ausgeschlossen werden. Oder man fasst ähnliche Variablen zusammen.


```r
wo_men %>% 
  select(height, shoe_size) %>% 
  correlate() -> km   # Korrelationsmatrix berechnen
km  
#> # A tibble: 2 × 3
#>     rowname height shoe_size
#>       <chr>  <dbl>     <dbl>
#> 1    height     NA     0.553
#> 2 shoe_size  0.553        NA

km %>% 
  shave() %>% # Oberes Dreieck ist redundant, wird "abrasiert"
  rplot()  # Korrelationsplot
```

<img src="043_Typische_Probleme_Datenanalyse_files/figure-html/unnamed-chunk-9-1.png" width="70%" style="display: block; margin: auto;" />

Die Funktion `correlate` stammt aus dem Paket `corrr`^[https://github.com/drsimonj/corrr ], welches vorher installiert und geladen sein muss. Hier ist die Korrelation nicht zu groß, so dass wir keine weiteren Schritte unternehmen.


### z-Standardisieren
Für eine Reihe von Analysen ist es wichtig, die Skalierung der Variablen zur vereinheitlichen. Die z-Standardisierung ist ein übliches Vorgehen. Dabei wird der Mittelwert auf 0 transformiert und die SD auf 1; man spricht - im Falle von (hinreichend) normalverteilten Variablen - jetzt von der *Standardnormalverteilung*\index{Standardnormalverteilung}. Unterscheiden sich zwei Objekte A und B in einer standardnormalverteilten Variablen, so sagt dies nur etwas zur relativen Position von A zu B innerhalb ihrer Verteilung aus - im Gegensatz zu den Rohwerten.


```r
wo_men %>% 
  select_if(is.numeric) %>%  # Spalte nur auswählen, wenn numerisch
  scale() %>%  # z-standardisieren
  head()  # nur die ersten paar Zeilen abdrucken
#>      height shoe_size
#> [1,] -0.132    0.0405
#> [2,]  0.146   -0.1395
#> [3,]  0.221   -0.1395
#> [4,]  0.272    0.0405
#> [5,]  0.751    1.1204
#> [6,] -0.208   -0.4994
```

Dieser Befehl liefert zwei z-standardisierte Spalten zurück. Kommoder ist es aber, alle Spalten des Datensatzes zurück zu bekommen, wobei zusätzlich die z-Werte aller numerischen Variablen hinzugekommen sind:


```r
wo_men %>% 
  mutate_if(is.numeric, funs("z" = scale)) %>% 
  head
#>                  time   sex height shoe_size height_z shoe_size_z
#> 1 04.10.2016 17:58:51 woman    160        40   -0.132      0.0405
#> 2 04.10.2016 17:58:59 woman    171        39    0.146     -0.1395
#> 3 04.10.2016 18:00:15 woman    174        39    0.221     -0.1395
#> 4 04.10.2016 18:01:17 woman    176        40    0.272      0.0405
#> 5 04.10.2016 18:01:22   man    195        46    0.751      1.1204
#> 6 04.10.2016 18:01:53 woman    157        37   -0.208     -0.4994
```

Der Befehl `mutate` berechnet eine neue Spalte; `mutate_if` tut dies, wenn die Spalte numerisch ist. Die neue Spalte wird berechnet als z-Transformierung der alten Spalte; zum Spaltenname wird ein "_z" hinzugefügt. Natürlich hätten wir auch mit `select` "händisch" die relevanten Spalten auswählen können.


### Quasi-Konstante finden
Hat eine Variable nur einen Wert, so verdient sie die Ehrenbezeichnung "Variable" nicht wirklich. Haben wir z.B. nur Männer im Datensatz, so kann das Geschlecht nicht für Unterschiede im Einkommen verantwortlich sein. Besser die Variable Geschlecht dann zu entfernen. Auch hier sind Histogramme oder Boxplots von Nutzen zur Identifiktion von (Quasi-)Konstanten. Alternativ kann man sich auch pro die Streuung (numerische Variablen) oder die Anzahl unterschiedlicher Werte (qualitative Variablen) ausgeben lassen.


### Auf Normalverteilung prüfen
Einige statistische Verfahren gehen von normalverteilten Variablen aus, daher macht es Sinn, Normalverteilung zu prüfen. *Perfekte* Normalverteilung ist genau so häufig, wie *perfekte* Kreise in der Natur. Entsprechend werden Signifikanztests, die ja auf perfekte Normalverteilung prüfen, immer signifikant sein, sofern die Stichrprobe groß genug ist. Daher ist meist zweckmäßiger, einen graphischen "Test" durchzuführen: Histogramm oder eine Dichte-Diagramm als "glatt geschmiergelte" Variante des Histogramms bieten sich an.

<img src="043_Typische_Probleme_Datenanalyse_files/figure-html/unnamed-chunk-12-1.png" width="70%" style="display: block; margin: auto;" />

Während die Körpergröße sehr deutlich normalverteilt ist, ist die Schuhgröße recht schief. Bei schiefen Verteilung können Transformationen Abhilfe schaffen. Hier erscheint die Schiefe noch erträglich, so dass wir keine weiteren Maßnahmen einleiten.


### Werte umkodieren und "binnen" 

*Umkodieren*\index{Umkodieren} meint, die Werte zu ändern. Man sieht immer mal wieder, dass die Variable "gender" (Geschlecht) mit `1` und `2` kodiert ist. Verwechslungen sind da vorpragmmiert ("Ich bin mir echt ziemlich sicher, dass ich 1 für Männer kodiert habe, wahrscheinlich..."). Besser wäre es, die Ausprägungen `male` und `female` ("Mann", "Frau") o.ä. zu verwenden (vgl. Abb. \@ref(fig:umkodieren)).

<div class="figure" style="text-align: center">
<img src="images/umkodieren_crop.pdf" alt="Sinnbild für Umkodieren" width="70%" />
<p class="caption">(\#fig:umkodieren)Sinnbild für Umkodieren</p>
</div>


*Binnen*\index{Binnen} meint, eine kontinuierliche Variablen in einige Bereiche (mindestens 2) zu zerschneiden. Ein Bild erläutert das am einfachsten (vgl. Abb. \@ref(fig:cut-schere)). 

<div class="figure" style="text-align: center">
<img src="images/cut_schere_crop.pdf" alt="Sinnbild zum 'Binnen'" width="70%" />
<p class="caption">(\#fig:cut-schere)Sinnbild zum 'Binnen'</p>
</div>



#### Umkodieren und binnen mit `car::recode`

Manchmal möchte man z.B. negativ gepolte Items umdrehen oder bei kategoriellen Variablen kryptische Bezeichnungen in sprechendere umwandeln. Hier gibt es eine Reihe praktischer Befehle, z.B. `recode` aus dem Paket `car`. Schauen wir uns ein paar Beispiele zum Umkodieren an.



```r

stats_test <- read.csv("data/test_inf_short.csv")

stats_test$score_fac <- car::recode(stats_test$study_time, "5 = 'sehr viel'; 2:4 = 'mittel'; 1 = 'wenig'", as.factor.result = TRUE)
stats_test$score_fac <- car::recode(stats_test$study_time, "5 = 'sehr viel'; 2:4 = 'mittel'; 1 = 'wenig'", as.factor.result = FALSE)

stats_test$study_time_2 <- car::recode(stats_test$study_time, "5 = 'sehr viel'; 4 = 'wenig'; else = 'Hilfe'", as.factor.result = TRUE)

head(stats_test$study_time_2)
#> [1] sehr viel Hilfe     sehr viel Hilfe     wenig     Hilfe    
#> Levels: Hilfe sehr viel wenig
```

Der Befehle `recode` ist praktisch; mit `:` kann man "von bis" ansprechen (das ginge mit `c()` übrigens auch); `else` für "ansonsten" ist möglich und mit `as.factor.result` kann man entweder einen Faktor oder eine Text-Variable zurückgeliefert bekommen. Der ganze "Wechselterm" steht in Anführungsstrichen (`"`). Einzelne Teile des Wechselterms sind mit einem Strichpunkt (`;`) voneinander getrennt.


Das klassiche Umkodieren von Items aus Fragebögen kann man so anstellen; sagen wir `interest` soll umkodiert werden:


```r
stats_test$no_interest <- car::recode(stats_test$interest, "1 = 6; 2 = 5; 3 = 4; 4 = 3; 5 = 2; 6 = 1; else = NA")
glimpse(stats_test$no_interest)
#>  num [1:306] 2 4 1 5 1 NA NA 4 2 2 ...
```

Bei dem Wechselterm muss man aufpassen, nichts zu verwechseln; die Zahlen sehen alle ähnlich aus...

Testen kann man den Erfolg des Umpolens mit


```r
dplyr::count(stats_test, interest)
#> # A tibble: 7 × 2
#>   interest     n
#>      <int> <int>
#> 1        1    30
#> 2        2    47
#> 3        3    66
#> 4        4    41
#> 5        5    45
#> 6        6     9
#> 7       NA    68
dplyr::count(stats_test, no_interest)
#> # A tibble: 7 × 2
#>   no_interest     n
#>         <dbl> <int>
#> 1           1     9
#> 2           2    45
#> 3           3    41
#> 4           4    66
#> 5           5    47
#> 6           6    30
#> 7          NA    68
```

Scheint zu passen. Noch praktischer ist, dass man so auch numerische Variablen in Bereiche aufteilen kann ("binnen"):



```r
stats_test$Ergebnis <- car::recode(stats_test$score, "1:38 = 'durchgefallen'; else = 'bestanden'")
```


Natürlich gibt es auch eine Pfeifen komptatible Version, um Variablen umzukodieren bzw. zu binnen: `dplyr::recode`^[https://blog.rstudio.org/2016/06/27/dplyr-0-5-0/]. Die Syntax ist allerdings etwas weniger komfortabel (da strenger), so dass wir an dieser Stelle bei `car::recode` bleiben.


#### Einfaches Umkodieren mit einer Logik-Prüfung

Nehmen wir an, wir möchten die Anzahl der Punkte in einer Statistikklausur (`score`) umkodieren in eine Variable "bestanden" mit den zwei Ausprägungen "ja" und "nein"; der griesgrämige Professor beschließt, dass die Klausur ab 25 Punkten (von 40) bestanden sei. Die Umkodierung ist also von der Art "viele Ausprägungen in zwei Ausprägungen umkodieren". Das kann man z.B. so erledigen:


```r
stats_test$bestanden <- stats_test$score > 24

head(stats_test$bestanden)
#> [1]  TRUE  TRUE  TRUE FALSE  TRUE  TRUE
```

Genauso könnte man sich die "Grenzfälle" - die Bemitleidenswerten mit 24 Punkten - anschauen (knapp daneben ist auch vorbei, so der griesgrämige Professor weiter):


```r
stats_test$Grenzfall <- stats_test$score == 24

count(stats_test, Grenzfall)
#> # A tibble: 2 × 2
#>   Grenzfall     n
#>       <lgl> <int>
#> 1     FALSE   294
#> 2      TRUE    12
```

Natürlich könnte man auch hier "Durchpfeifen":


```r
stats_test <- 
stats_test %>% 
  mutate(Grenzfall = score == 24)

count(stats_test, Grenzfall)
#> # A tibble: 2 × 2
#>   Grenzfall     n
#>       <lgl> <int>
#> 1     FALSE   294
#> 2      TRUE    12
```


#### Binnen mit `cut`
Numerische Werte in Klassen zu gruppieren ("to bin", denglisch: "binnen") kann mit dem Befehl `cut` (and friends) besorgt werden. 

Es lassen sich drei typische Anwendungsformen unterscheiden:

Eine numerische Variable ...

1. in *k* gleich große Klassen grupieren (gleichgroße Intervalle)
2. so in Klassen gruppieren, dass in jeder Klasse *n* Beobachtungen sind (gleiche Gruppengrößen)
3. in beliebige Klassen gruppieren


##### Gleichgroße Intervalle

Nehmen wir an, wir möchten die numerische Variable "Körpergröße" in drei Gruppen einteilen: "klein", "mittel" und "groß". Der Range von Körpergröße soll gleichmäßig auf die drei Gruppen aufgeteilt werden, d.h. der Range (Interval) der drei Gruppen soll gleich groß sein. Dazu kann man `cut_interval` aus `ggplot2` nehmen [^d.h. `ggplot2` muss geladen sein; wenn man `tidyverse` lädt, wird `ggplot2` automatisch auch geladen].


```r
wo_men <- read_csv("data/wo_men.csv")

wo_men %>% 
  filter(height > 150, height < 220) -> wo_men2

temp <- cut_interval(x = wo_men2$height, n = 3)

levels(temp)
#> [1] "[155,172]" "(172,189]" "(189,206]"
```

`cut_interval` liefert eine Variabel vom Typ `factor` zurück. 


##### Gleiche Gruppengrößen


```r
temp <- cut_number(wo_men2$height, n = 2)
str(temp)
#>  Factor w/ 2 levels "[155,169]","(169,206]": 1 2 2 2 2 1 1 2 1 2 ...
```

Mit `cut_number` (aus ggplot2) kann man einen Vektor in `n` Gruppen mit (etwa) gleich viel Observationen einteilen.

>   Teilt man einen Vektor in zwei gleich große Gruppen, so entspricht das einer Aufteilung am Median (Median-Split).


##### In beliebige Klassen gruppieren


```r
wo_men$groesse_gruppe <- cut(wo_men$height, 
                             breaks = c(-Inf, 100, 150, 170, 200, 230, Inf))

count(wo_men, groesse_gruppe)
#> # A tibble: 6 × 2
#>   groesse_gruppe     n
#>           <fctr> <int>
#> 1     (-Inf,100]     4
#> 2      (150,170]    55
#> 3      (170,200]    38
#> 4      (200,230]     2
#> 5     (230, Inf]     1
#> 6             NA     1
```

`cut` ist im Standard-R (Paket "base") enthalten. Mit `breaks` gibt man die Intervallgrenzen an. Zu beachten ist, dass man eine Unter- bzw. Obergrenze angeben muss. D.h. der kleinste Wert in der Stichprobe wird nicht automatisch als unterste Intervallgrenze herangezogen. Anschaulich gesprochen ist `cut` ein Messer, das ein Seil (die kontinuierliche Variable) mit einem oder mehreren Schnitten zerschneidet (vgl. Abb. \@ref(fig:bin-schere)).



## Deskriptive Statistiken berechnen


### Mittelwerte pro Zeile berechnen

#### `rowMeans`
Um Umfragedaten auszuwerten, will man häufig einen Mittelwert *pro Zeile* berechnen. Normalerweise fasst man eine *Spalte* zu einer Zahl zusammen; aber jetzt, fassen wir eine *Zeile* zu einer Zahl zusammen. Der häufigste Fall ist, wie gesagt, einen Mittelwert zu bilden für jede Person. Nehmen wir an, wir haben eine Befragung zur Extraversion durchgeführt und möchten jetzt den mittleren Extraversions-Wert pro Person (d.h. pro Zeile) berechnen.


```r
extra <- read.csv("data/extra.csv")

extra_items <- extra %>% 
  select(i01:i10)  # `select` ist aus `dplyr`

# oder:
# select(extra_items, i01:i10)

extra$extra_mw <- rowMeans(extra_items)
```

Da der Datensatz über 28 Spalten verfügt, wir aber nur 10 Spalten heranziehen möchten, um Zeilen auf eine Zahl zusammenzufassen, bilden wir als Zwischenschritt einen "schmäleren" Datensatz, `extra_items`. Im Anschluss berechnen wir mit `rowMeans` die Mittelwerte pro Zeile (engl. "row").


#### Vertiefung: `dpyr`

Alternativ können wir Mittelwerte mit dplyr berechnen:



```r
extra_items %>% 
  na.omit %>% 
  rowwise() %>% 
  mutate(mean_row = mean(i01:i10)) %>% 
  select(mean_row) %>% 
  head # nur die ersten paar Zeilen von `mean_row` zeigen
#> # A tibble: 6 × 1
#>   mean_row
#>      <dbl>
#> 1      2.0
#> 2      1.5
#> 3      2.0
#> 4      2.5
#> 5      4.0
#> 6      3.0
```

`na.omit` wirft alle Zeilen raus, in denen fehlende Werte vorkommen. Das ist nötig, damit `mean` ein Ergebnis ausgibt (bei fehlenden Werten gibt `mean` sonst `NA` zurück).

`rowwise` gruppiert den Datensatz nach Zeilen (`row_number()`), ist also synonym zu:


```r
extra_items %>% 
  na.omit %>% 
  group_by(row_number()) %>% 
  mutate(mean_row = mean(i01:i10)) %>% 
  select(mean_row) %>% 
  head # nur die ersten paar Zeilen von `mean_row` zeigen
#> Source: local data frame [6 x 2]
#> Groups: row_number() [6]
#> 
#>   `row_number()` mean_row
#>            <int>    <dbl>
#> 1              1      2.0
#> 2              2      1.5
#> 3              3      2.0
#> 4              4      2.5
#> 5              5      4.0
#> 6              6      3.0
```


### Mittelwerte pro Spalte berechnen


Eine Möglichkeit ist der Befehl `summary` aus `dplyr`.


```r
stats_test %>% 
  summarise(mean(score),
            sd(score),
            median(score),
            IQR(score))
#>   mean(score) sd(score) median(score) IQR(score)
#> 1        31.1      5.74            31          9
```

Die Logik von `dplyr` lässt auch einfach Subgruppenanalysen zu. Z.B. können wir eine Teilmenge des Datensatzes mit `filter` erstellen und dann mit `group_by` Gruppen vergleichen:


```r
stats_test %>% 
  filter(study_time > 1) %>% 
  group_by(interest) %>% 
  summarise(mean(score))
#> # A tibble: 0 × 2
#> # ... with 2 variables: interest <int>, mean(score) <dbl>
```


Wir können auch Gruppierungskriterien unterwegs erstellen:



```r
stats_test %>% 
  filter(study_time > 1) %>% 
  group_by(intessiert = interest > 3) %>% 
  group_by(intessiert) %>% 
  summarise(mean(score))
#> # A tibble: 0 × 2
#> # ... with 2 variables: intessiert <lgl>, mean(score) <dbl>
```

Die beiden Gruppen von `interessiert` sind "ja, interessiert" (`interest > 3` ist `TRUE`) und "nein, nicht interessiert" (`interest > 3` ist `FALSE`).


### Korrelationstabellen berechnen

Korrelationen bzw. Korrelationstabellen lassen sich mit dem R-Standardbefehl `cor` berechnen:


```r
stats_test <- read.csv("data/test_inf_short.csv")

stats_test %>% 
  select(study_time,interest,score) %>% 
  cor()
#>            study_time interest score
#> study_time          1       NA    NA
#> interest           NA        1    NA
#> score              NA       NA     1
```


Oh! Lauter NAs! Besser wir löschen Zeilen mit fehlenden Werten bevor wir die Korrelation ausrechnen:



```r
stats_test %>% 
  select(study_time:score) %>% 
  na.omit %>% 
  cor()
#>            study_time self_eval interest score
#> study_time      1.000     0.559    0.461 0.441
#> self_eval       0.559     1.000    0.360 0.628
#> interest        0.461     0.360    1.000 0.223
#> score           0.441     0.628    0.223 1.000
```


Alternativ zu `cor` kann man auch `corrr:correlate` verwenden:


```r
stats_test <- read.csv("data/test_inf_short.csv")


stats_test %>% 
  select(study_time:score) %>% 
  correlate
#> # A tibble: 4 × 5
#>      rowname study_time self_eval interest score
#>        <chr>      <dbl>     <dbl>    <dbl> <dbl>
#> 1 study_time         NA     0.559    0.461 0.441
#> 2  self_eval      0.559        NA    0.360 0.628
#> 3   interest      0.461     0.360       NA 0.223
#> 4      score      0.441     0.628    0.223    NA
```


`correlate` hat den Vorteil, dass es bei fehlenden Werten einen Wert ausgibt; die Korrelation wird paarweise mit den verfügbaren (nicht-fehlenden) Werten berechnet. Außerdme wird eine Dataframe (genauer: tibble) zurückgeliefert, was häufig praktischer ist zur Weiterverarbeitung. Wir könnten jetzt die resultierende Korrelationstabelle plotten, vorher "rasieren" wir noch das redundaten obere Dreieck ab (da Korrelationstabellen ja symmetrisch sind):



```r
stats_test %>% 
  select(study_time:score) %>% 
  correlate %>% 
  shave %>% 
  rplot
```

<img src="043_Typische_Probleme_Datenanalyse_files/figure-html/rplot-demo-1.png" width="70%" style="display: block; margin: auto;" />


## Befehlsübersicht


Paket::Funktion        Beschreibung
-----------------      -------------
na.omit                Löscht Zeilen, die fehlende Werte enthalten
nrow                   Liefert die Anzahl der Zeilen des Dataframes zurück  
complete.cases         Gibt die Zeilen ohne fehlenden Werte eines Dataframes zurück
car::recode            Kodiert Werte um
cut                    Schneidet eine kontinuierliche Variable in Wertebereiche
rowMeans               Berechnet Zeilen-Mittelwerte
dplyr::rowwise         Gruppiert nach Zeilen
ggplot2::cut_number    Schneidet eine kontinuierliche Variable in *n* gleich große Bereiche
ggplot2::cut_interval  Schneidet eine kontinuierliche Variable in Intervalle der Größe *k*
head                   Zeigt nur die ersten Zeilen/Werte eines Dataframes/Vektors an.  
scale                  z-skaliert eine Variable
dplyr::select_if       Wählt eine Spalte aus, wenn ein Kriterium erfüllt ist
dplyr::glimpse         Gibt einen Überblick über einen Dataframe
dplyr::mutate_if       definiert eine Spalte, wenn eine Kriterium erfüllt ist
:                      Definiert einen Bereich von ... bis ...
corrr:correlate        Berechnet Korrelationtabelle, liefert einen Dataframe zurück
cor                    Berechnet Korrelationtabelle
rplot                  Plottet Korrelationsmatrix von `correlate`
shave                  "Rasiert" redundantes Dreick in Korrelationsmatrix ab