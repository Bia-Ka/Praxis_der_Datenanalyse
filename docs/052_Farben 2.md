
# Farben wählen

Benötigte Pakete:

```r
library(tidyverse)  # Zum Plotten
```

```
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
## Loading tidyverse: dplyr
```

```
## Conflicts with tidy packages ----------------------------------------------
```

```
## filter(): dplyr, stats
## lag():    dplyr, stats
```

```r
library(wesanderson)  # Farb-Palette von Wes Anderson
library(RColorBrewer)  # Farb-Palette von Cynthia Brewer
library(knitr)  # für HTML-Tabellen
library(gridExtra)  # für kombinierte Plots
```

```
## 
## Attaching package: 'gridExtra'
```

```
## The following object is masked from 'package:dplyr':
## 
##     combine
```


Erstens, nicht schaden - so könnte hier die Maßregel der der Wahl von Farben sein. Es ist leicht, zu grelle oder wenig kontrastierende Farben auszuwählen. Eine gute Farbauswahl (Palette) ist nicht so leicht und hängt vom Zweck der Darstellung ab.

Cynthia Brewer^[http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3] hat einige schöne Farbpaletten zusammengestellt; diese sind in R und in ggplot2 über das Paket `RcolorBrewer` verfügbar. 


```r
brewer.pal.info %>% rownames_to_column %>% rename(Name = rowname) %>% kable
```



Name        maxcolors  category   colorblind 
---------  ----------  ---------  -----------
BrBG               11  div        TRUE       
PiYG               11  div        TRUE       
PRGn               11  div        TRUE       
PuOr               11  div        TRUE       
RdBu               11  div        TRUE       
RdGy               11  div        FALSE      
RdYlBu             11  div        TRUE       
RdYlGn             11  div        FALSE      
Spectral           11  div        FALSE      
Accent              8  qual       FALSE      
Dark2               8  qual       TRUE       
Paired             12  qual       TRUE       
Pastel1             9  qual       FALSE      
Pastel2             8  qual       FALSE      
Set1                9  qual       FALSE      
Set2                8  qual       TRUE       
Set3               12  qual       FALSE      
Blues               9  seq        TRUE       
BuGn                9  seq        TRUE       
BuPu                9  seq        TRUE       
GnBu                9  seq        TRUE       
Greens              9  seq        TRUE       
Greys               9  seq        TRUE       
Oranges             9  seq        TRUE       
OrRd                9  seq        TRUE       
PuBu                9  seq        TRUE       
PuBuGn              9  seq        TRUE       
PuRd                9  seq        TRUE       
Purples             9  seq        TRUE       
RdPu                9  seq        TRUE       
Reds                9  seq        TRUE       
YlGn                9  seq        TRUE       
YlGnBu              9  seq        TRUE       
YlOrBr              9  seq        TRUE       
YlOrRd              9  seq        TRUE       


- Kontrastierende Darstellung (nominale/ qualitative Variablen) - z.B. Männer vs. Frauen


```r
display.brewer.all(type="qual")
```

<img src="052_Farben_files/figure-html/unnamed-chunk-2-1.png" width="100%" />

- Sequenzielle Darstellung (unipolare numerische Variablen) - z.B. Preis oder Häufigkeit

```r
display.brewer.all(type="seq")
```

<img src="052_Farben_files/figure-html/unnamed-chunk-3-1.png" width="100%" />


- Divergierende Darstellung (bipolare numerische Variablen) - z.B. semantische Potenziale oder Abstufung von "stimme überhaupt nicht zu" über "neutral" bis "stimme voll und ganz zu"


```r
display.brewer.all(type="div")
```

<img src="052_Farben_files/figure-html/unnamed-chunk-4-1.png" width="100%" />

In `ggplot2` können wir folgendermaßen Paletten ändern (dazu laden wir den Datensatz `flights` noch einmal, falls Sie ihn nicht mehr geladen haben).


```r
library(nycflights13)
data(flights)

flights %>% 
  group_by(dest) %>% 
  count(dest) %>% 
  top_n(5)
```

```
## Selecting by n
```

```
## # A tibble: 5 × 2
##    dest     n
##   <chr> <int>
## 1   ATL 17215
## 2   BOS 15508
## 3   LAX 16174
## 4   MCO 14082
## 5   ORD 17283
```

```r
p1 <- flights %>% 
  filter(dest %in% c("BOS", "ATL", "LAX")) %>% 
  ggplot() +
  aes(x = dest, y = arr_delay, color = dest) +
  geom_boxplot() +
  scale_color_brewer(palette = "Set1")

p2 <- flights %>% 
  filter(dest %in% c("BOS", "ATL", "LAX", "MCO", "ORD")) %>% 
  ggplot() +
  aes(x = dest, y = arr_delay, fill = dest) +
  geom_boxplot() +
  scale_fill_brewer(palette = "Set1")

grid.arrange(p1, p2, ncol = 2)
```

```
## Warning: Removed 1012 rows containing non-finite values (stat_boxplot).
```

```
## Warning: Removed 1844 rows containing non-finite values (stat_boxplot).
```

<img src="052_Farben_files/figure-html/brewerpal-1.png" width="100%" />

`scale_color_brewer` meint hier: "Ordne der Variablen, die für 'color' zuständig ist, hier `sex`, eine Farbe aus der Brewer-Palette 'Set1' zu". Die Funktion wählt *automatisch* die richtige Anzahl von Farben.

Man beachte, dass die Linienfarbe über `color` und die Füllfarbe über `fill` zugewiesen wird. Punkte haben nur eine Linienfarbe, keine Füllfarbe.



Auch die Farbpaletten von Wes Anderson sind erbaulich^[https://github.com/karthik/wesanderson]. Diese sind nicht "hart verdrahtet" in ggplot2, sondern werden über `scale_XXX_manual` zugewiesen (wobei XXX z.B. `color` oder `fill` sein kann).


```r
data(tips, package = "reshape2")

p1 <- tips %>% 
  ggplot() +
  aes(x = total_bill, y = tip, color = day) +
  geom_point() +
  scale_color_manual(values = wes_palette("GrandBudapest")) +
  theme(legend.position = "bottom")

p2 <- tips %>% 
  ggplot() +
  aes(x = total_bill, y = tip, color = day) +
  geom_point() +
  scale_color_manual(values = wes_palette("Chevalier"))  +
  theme(legend.position = "bottom")

meine_farben <- c("red", "blue", "#009981", "#32F122")

p3 <- tips %>% 
  ggplot() +
  aes(x = total_bill, y = tip, color = day) +
  geom_point() +
  scale_color_manual(values = meine_farben)  +
  theme(legend.position = "bottom")

grid.arrange(p1, p2, p3, ncol = 3)
```

<img src="052_Farben_files/figure-html/unnamed-chunk-5-1.png" width="672" />


Wer sich berufen fühlt, eigene Farben (oder die seiner Organisation zu verwenden), kommt auf ähnlichem Weg zu Ziel. Man definiere sich seine Palette, wobei ausreichend Farben definiert sein müssen. Diese weist man dann über `scale_XXX_manual` dann zu. Man kann einerseits aus den in R definierten Farben auswählen^[http://sape.inf.usi.ch/quick-reference/ggplot2/colour] oder sich selber die RBG-Nummern (in Hexadezimal-Nummern) heraussuchen.

