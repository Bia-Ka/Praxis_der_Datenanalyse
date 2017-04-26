



# Daten einlesen


\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">Lernziele:

- Wissen, was eine CSV-Datei ist.
- Wissen, was UTF-8 bedeutet.
- Erläutern können, was R unter dem "working directory" versteht.
- Erkennen können, ob eine Tabelle in Normalform vorliegt.
- Daten aus R hinauskriegen (exportieren).

</div>\EndKnitrBlock{rmdcaution}

Dieses Kapitel beantwortet eine Frage: "Wie kriege ich Daten in vernünftiger Form in R hinein?".

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{images/Einlesen} 

}

\caption{Daten sauber einlesen}(\#fig:step-Einlesen)
\end{figure}


## Daten in R importieren
In R kann man ohne Weiteres verschiedene, gebräuchliche (Excel oder CSV) oder weniger
gebräuchliche (Feather^[<https://cran.r-project.org/web/packages/feather/index.html>]) Datenformate einlesen. In RStudio lässt sich dies
z.B. durch einen schnellen Klick auf `Import Dataset` im Reiter `Environment`
erledigen^[Um CSV-Dateien zu laden wird durch den Klick im Hintergrund das Paket `readr` verwendet [@readr];
die entsprechende Syntax wird in der Konsole ausgegeben, so dass man sie sich
anschauen und weiterverwenden kann].


### Excel-Dateien importieren

Am einfachsten ist es, eine Excel-Datei (.xls oder .xlsx) über die RStudio-Oberfläche zu importieren; das ist mit ein paar Klicks geschehen^[im Hintergrund wird das Paket `readxl` verwendet]:

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{images/import_RStudio} 

}

\caption{Daten einlesen (importieren) mit RStudio}(\#fig:data-import-RStudio)
\end{figure}



Es ist für bestimmte Zwecke sinnvoll, nicht zu klicken, sondern die Syntax einzutippen. Zum Beispiel: Wenn Sie die komplette Analyse als Syntax in einer Datei haben (eine sog. "Skriptdatei"), dann brauchen Sie (in RStudio) nur alles auszuwählen und auf `Run` zu klicken, und die komplette Analyse läuft durch! Die Erfahrung zeigt, dass das ein praktisches Vorgehen ist.


\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">
Daten (CSV, Excel,...)  können Sie *nicht* öffnen über `File > Open File ...`. Dieser Weg ist Skript-Dateien vorbehalten. 
</div>\EndKnitrBlock{rmdcaution}


### CSV-Dateien importieren

Die gebräuchlichste Form von Daten für statistische Analysen ist wahrscheinlich das CSV-Format. Das ist ein einfaches Format, basierend auf einer Textdatei. Schauen Sie sich mal diesen Auszug aus einer CSV-Datei an.

```
"ID","time","sex","height","shoe_size"
"1","04.10.2016 17:58:51",NA,160.1,40
"2","04.10.2016 17:58:59","woman",171.2,39
"3","04.10.2016 18:00:15","woman",174.2,39
"4","04.10.2016 18:01:17","woman",176.4,40
"5","04.10.2016 18:01:22","man",195.2,46
```

Erkennen Sie das Muster? Die erste Zeile gibt die "Spaltenköpfe" wieder, also die Namen der Variablen. Hier sind es 5 Spalten; die vierte heißt "shoe_size". Die Spalten sind offenbar durch Komma `,` voneinander getrennt. Dezimalstellen sind in amerikanischer Manier mit einem Punkt `.` dargestellt. Die Daten sind "rechteckig"; alle Spalten haben gleich viele Zeilen und umgekehrt alle Spalten gleich viele Zeilen. Man kann sich diese Tabelle gut als Excel-Tabelle mit Zellen vorstellen, in denen z.B. "ID" (Zelle oben links) oder "46" (Zelle unten rechts) steht.

An einer Stelle steht `NA`. Das ist Errisch für "fehlender Wert". Häufig wird die Zelle auch leer gelassen, um auszudrücken, dass ein Wert hier fehlt (hört sich nicht ganz doof an). Aber man findet alle möglichen Ideen, um fehlende Werte darzustellen. Ich rate von allen anderen ab; führt nur zu Verwirrung.

Lesen wir diese Daten jetzt ein:



```r
daten <- read.csv("data/wo_men.csv")
```



Der Befehl `read.csv` liest also eine CSV-Datei, was uns jetzt nicht übermäßig überrascht. Aber Achtung: Wenn Sie aus einem Excel mit deutscher Einstellung eine CSV-Datei exportieren, wird diese CSV-Datei als Trennzeichen `;` (Strichpunkt) und als Dezimaltrennzeichen `,` verwenden. Da der Befehl `read.csv` als Standard mit Komma und Punkt arbeitet, müssen wir die deutschen Sonderlocken explizit angeben, z.B. so:


```r
# nicht ausführen:
daten_deutsch <- read.csv("daten_deutsch.csv", sep = ";", dec = ".")
```

Dabei steht `sep` (separator) für das Trennzeichen zwischen den Spalten und `dec` für das Dezimaltrennzeichen. R bietet eine Kurzfassung für `read.csv` mit diesen Parametern: `read.csv2("daten_deutsch.csv")`.

### Vertiefung: Einlesen mit Prüfung


```
#>   X                time   sex height shoe_size
#> 1 1 04.10.2016 17:58:51 woman    160        40
#> 2 2 04.10.2016 17:58:59 woman    171        39
#> 3 3 04.10.2016 18:00:15 woman    174        39
#> 4 4 04.10.2016 18:01:17 woman    176        40
#> 5 5 04.10.2016 18:01:22   man    195        46
#> 6 6 04.10.2016 18:01:53 woman    157        37
```

Wir haben zuerst geprüft, ob die Datei (`wo_men.csv`) im entsprechenden Ordner existiert oder nicht (das `!`-Zeichen heißt auf Errisch "nicht"). Falls die Datei nicht im Ordner existiert, laden wir sie mit `read.csv` herunter und direkt ins R hinein. Andernfalls (`else`) lesen wir sie direkt ins R hinein.



### Das Arbeitsverzeichnis

\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">
Übrigens: Wenn Sie keinen Pfad angeben, so geht R davon aus, dass die Daten im aktuellen Verzeichnis (dem *working directory*) liegen. 
</div>\EndKnitrBlock{rmdcaution}

Das aktuelle Verzeichnis (Arbeitsverzeichnis; "working directory") kann man mit `getwd()` erfragen und mit `setwd()` einstellen. Komfortabler ist es aber, das aktuelle Verzeichnis per Menü zu ändern. In RStudio: `Session > Set Working Directory > Choose Directory ...` (oder per Shortcut, der dort angezeigt wird).

Es ist praktisch, das Arbeitsverzeichnis festzulegen, denn dann kann man z.B. eine Datendatei einlesen, ohne den Pfad eingeben zu müssen:


```r
# nicht ausführen:
daten_deutsch <- read.csv("daten_deutsch.csv", sep = ";", dec = ".")
```

R geht dann davon aus, dass sich die Datei `daten_deutsch.csv` im Arbeitsverzeichnis befindet.

## Normalform einer Tabelle
Tabellen in R werden als `data frames` ("Dataframe" auf Denglisch; moderner: als `tibble`, Tibble kurz für "Table-df") bezeichnet. Tabellen sollten in "Normalform" vorliegen ("tidy"), bevor wir weitere Analysen starten. Unter Normalform verstehen sich folgende Punkte:

- Es handelt sich um einen Dataframe, also um eine Tabelle mit Spalten mit Namen und gleicher Länge; eine Datentabelle in rechteckiger Form und die Spalten haben einen Namen.
- In jeder Zeile steht eine Beobachtung, in jeder Spalte eine Variable.
- Fehlende Werte sollten sich in *leeren* Zellen niederschlagen.
- Daten sollten nicht mit Farbmarkierungen o.ä. kodiert werden.
- Es gibt keine Leerzeilen und keine Leerspalten.
- In jeder Zelle steht ein Wert.
- Am besten verwendet man keine Sonderzeichen verwenden und keine Leerzeichen in Variablennamen und -werten, sondern nur Ziffern und Buchstaben und Unterstriche.
- Variablennamen dürfen nicht mit einer Zahl beginnen.

Abbildung \@ref(fig:tidy1) visualisiert die Bestimmungsstücke eines Dataframes [@r4ds]: 

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{images/tidy-1} 

}

\caption{Schematische Darstellung eines Dataframes in Normalform}(\#fig:tidy1)
\end{figure}



Der Punkt "Jede Zeile eine Beobachtung, jede Spalte eine Variable" verdient besondere Beachtung. Betrachten Sie dieses Beispiel:

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{images/breit_lang} 

}

\caption{Dieselben Daten - einmal breit, einmal lang}(\#fig:lang-breit)
\end{figure}


In der rechten Tabelle sind die Variablen `Quartal` und `Umsatz` klar getrennt; jede hat ihre eigene Spalte. In der linken Tabelle hingegen sind die beiden Variablen vermischt. Sie haben nicht mehr ihre eigene Spalte, sondern sind über vier Spalten verteilt. Die rechte Tabelle ist ein Beispiel für eine Tabelle in Normalform, die linke nicht.


\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{images/Normalform} 

}

\caption{Illustration eines Datensatzes in Normalform}(\#fig:fig-Normalform)
\end{figure}

## Vertiefung

### Tabelle in Normalform bringen

Eine der ersten Aktionen einer Datenanalyse sollte also die "Normalisierung" Ihrer Tabelle sein. In R bietet sich dazu das Paket `tidyr` an, mit dem die Tabelle von Breit- auf Langformat (und wieder zurück) geschoben werden kann.

Ein Beispiel dazu:


```r
meindf <- read.csv("http://stanford.edu/~ejdemyr/r-tutorials/data/unicef-u5mr.csv")

df_lang <- gather(meindf, year, u5mr, U5MR.1950:U5MR.2015)

df_lang <- separate(df_lang, year, into = c("U5MR", "year"), sep = ".")
```

- Die erste Zeile liest die Daten aus einer CSV-Datei ein; praktischerweise direkt von einer Webseite.   
- Die zweite Zeile `gather` formt die Daten *von breit nach lang* um. Die neuen Spalten, nach der Umformung heißen dann `year` und `u5mr` (Sterblichkeit bei Kindern unter fünf Jahren). In die Umformung werden die Spalten `U5MR 1950` bis `U5MR 2015` einbezogen.
- Die dritte Zeile `separate` *entzerrt* die Werte der Spalte `year`; hier stehen die ehemaligen Spaltenköpfe. Man nennt sie auch `key` Spalte daher. Steht in einer Zelle von `year` bspw. `U5MR 1950`, so wird `U5MR` in eine Spalte mit Namen `U5MR` und `1950` in eine Spalte mit Namen `year` geschrieben.


### Textkodierung

Öffnet man eine Textdatei mit einem Texteditor seiner Wahl, so sieht man... Text und sonst nichts, also keine Formatierung etc. Eine Textdatei besteht aus Text und sonst nichts (daher der Name...). Auch eine R-Skript-Datei (`Coole_Syntax.R`) ist eine Textdatei.
Technisch gesprochen werden nur die Textzeichen gespeichert, sonst nichts; im Gegensatz dazu speichert eine Word-Datei noch mehr, z.B. Formatierung. Ein bestimmtes Zeichen wie "A" bekommt einen bestimmten Code wie "41". Mit etwas Glück weiß der Computer jetzt, dass er das Zeichen "41" auf den Bildschirm ausgeben soll. Es stellt sich jetzt die Frage, welche Code-Tabelle der Computer nutzt? Welchem Code wird "A" (bzw. ein beliebiges Zeichen) zugeordnet? Mehrere solcher Kodierungstafeln existieren. Die gebräuchlichste im Internet heißt *UTF-8*^[https://de.wikipedia.org/wiki/UTF-8]. Leider benutzen unterschiedliche Betriebssysteme unterschiedliche Kodierungstafeln, was zu Verwirrung führt. Ich empfehle, ihre Textdateien als UTF-8 zu kodieren. RStudio fragt sie, wie eine Textdatei kodiert werden soll. Sie können auch unter `File > Save with Encoding...` die Kodierung einer Textdatei festlegen.

>    Speichern Sie R-Textdateien wie Skripte stets mit UTF-8-Kodierung ab.


### Daten exportieren

Wie bekommt man seine Daten wieder aus R raus ("ich will zu Excel zurück!")?

Eine Möglichkeit bietet die Funktion `write.csv`; sie schreibt eine CSV-Datei:

```
write.csv(name_der_tabelle, "Dateiname.csv")
```

Mit `help(write.csv)` bekommt man mehr Hinweise dazu. Beachten Sie, dass immer in das aktuelle Arbeitsverzeichnis geschrieben wird.



## Befehlsübersicht

Paket::Funktion      Beschreibung
-----------------    -------------
read.csv             Liest eine CSV-Datei ein.
write.csv            Schreibt einen Dateframe in eine CSV-Datei.
readr::gather        Macht aus einem "breiten" Dataframe einen "langen".
readr::separate      "Zieht" Spalten auseinander.  




## Übungen^[F, R, F, R, R, R, F, F]

\BeginKnitrBlock{rmdexercises}<div class="rmdexercises">Richtig oder Falsch!?

1. In CSV-Dateien dürfen Spalten *nie* durch Komma getrennt sein.
2. RStudio bietet die Möglichkeit, CSV-Dateien per Klick zu importieren.
2. RStudio bietet *nicht* die Möglichkeit, CSV-Dateien per Klick zu importieren.
2. "Deutsche" CSV-Dateien verwenden als Spalten-Trennzeichen einen Strichpunkt.
2. In einer Tabelle in Normalform stehen in jeder Zeile eine Beobachtung.
2. In einer Tabelle in Normalform stehen in jeder Spalte eine Variable.
2. R stellt fehlende Werte mit einem Fragezeichen `?` dar.
2. Um Excel-Dateien zu importieren, kann man den Befehl `read.csv` verwenden.


</div>\EndKnitrBlock{rmdexercises}




## Verweise

- *R for Data Science* bietet umfangreiche Unterstützung zu diesem Thema [@r4ds].

