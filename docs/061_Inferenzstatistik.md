

# Der p-Wert


\BeginKnitrBlock{rmdcaution}<div class="rmdcaution">Lernziele:

- Den p-Wert erläutern können.
- Den p-Wert kritisieren können.
</div>\EndKnitrBlock{rmdcaution}



<div class="figure" style="text-align: center">
<img src="images/Ronald_Fisher.jpg" alt="Der größte Statistiker des 20. Jahrhunderts (p &lt; .05)" width="20%" />
<p class="caption">(\#fig:sir-fisher)Der größte Statistiker des 20. Jahrhunderts (p < .05)</p>
</div>

## Der p-Wert sagt nicht das, was viele denken


Der p-Wert, entwickelt von Sir Ronald Fisher (Abb. \@ref(fig:sir-fisher)), ist die heilige Kuh der Forschung. Das ist nicht normativ, sondern deskriptiv gemeint. Der p-Wert entscheidet (häufig) darüber, was publiziert wird, und damit, was als Wissenschaft sichtbar ist - und damit, was Wissenschaft ist (wiederum deskriptiv, nicht normativ gemeint). Kurz: Dem p-Wert wird viel Bedeutung zugemessen (vgl. Abb. \@ref(fig:who-said)). 


<div class="figure" style="text-align: center">
<img src="images/p_value_who_said.png" alt="Der p-Wert wird oft als wichtig erachtet" width="35%" />
<p class="caption">(\#fig:who-said)Der p-Wert wird oft als wichtig erachtet</p>
</div>


Was sagt uns der p-Wert? Eine gute intuitive Definition ist:

>    Der p-Wert sagt, wie gut die Daten zur Nullhypothese passen.


Je größer p, desto besser passen die Daten zur Nullhypothese.

Allerdings hat der p-Wert seine Probleme. Vor allem: Er wird missverstanden. Jetzt kann man sagen, dass es dem p-Wert (dem armen) nicht anzulasten, dass andere/ einige ihm missverstehen. Auf der anderen Seite finde ich, dass sich Technologien dem Nutzer anpassen sollten (soweit als möglich) und nicht umgekehrt. Die (genaue) Definition des p-Werts ist aber auch so kompliziert, man kann sie leicht missverstehen:

> Der p-Wert gibt die Wahrscheinlichkeit P unserer Daten D an (und noch extremerer), unter der Annahme, dass die getestete Hypothese H wahr ist (und wenn wir den Versuch unendlich oft wiederholen würden, unter identischen Bedingungen und ansonsten zufällig).
p = P(D|H)

Viele Menschen - inkl. Professoren und Statistik-Dozenten - haben Probleme mit dieser Definition [@Gigerenzer2004]. Das ist nicht deren Schuld: Die Definition ist kompliziert. Vielleicht denken viele, der p-Wert sage das, was tatsächlich interessant ist: die Wahrscheinlichkeit der (getesteten) Hypothese H, gegeben der Tatsache, dass bestimmte Daten D vorliegen. Leider ist das *nicht* die Definition des p-Werts. Also:

$$ P(D|H) \ne P(H|D) $$

### Von Moslems und Terroristen

Formeln haben die merkwürdige Angewohnheit vor dem inneren Auge zu verschwimmen; Bilder sind für viele Menschen klarer, scheint's. Übersetzen wir die obige Formel in folgenden Satz:

>   Wahrscheinlichkeit, Moslem zu sein, wenn man Terrorist ist UNGLEICH zur 
Wahrscheinlichkeit, Terrorist zu sein, wenn man Moslem ist.


Oder kürzer:


$$ P(M|T) \ne P(T|M) $$




<div class="figure" style="text-align: center">
<img src="images/moslems_terroristen.jpeg" alt="Moslem und Terrorist zu sein, ist nicht das gleiche." width="70%" />
<p class="caption">(\#fig:moslems-terroristen)Moslem und Terrorist zu sein, ist nicht das gleiche.</p>
</div>


Das Bild (Abb. \@ref(fig:moslems-terroristen)) zeigt den Anteil der Moslems an den Terroristen (sehr hoch). Und es zeigt den Anteil der Terroristen von allen Moslems (sehr gering). Dabei können wir uns Anteil mit Wahrscheinlichkeit übersetzen. Kurz: Die beiden Anteile (Wahrscheinlichkeiten) sind nicht gleich. Man denkt leicht, der p-Wert sei die *Wahrscheinlichkeit, Terrorist zu sein, wenn man Moslem ist*. Das ist falsch. Der p-Wert ist die *Wahrscheinlichkeit, Moslem zu sein, wenn man Terrorist ist*. Ein großer Unterschied^[die Größe der Anteile sind frei erfunden].


## Der p-Wert ist eine Funktion der Stichprobengröße

Der p-Wert ist für weitere Dinge kritisiert worden [@Wagenmakers2007, @uncertainty]; z.B. dass die "5%-Hürde" einen zu schwachen Test für die getestete Hypothese bedeutet. Letzterer Kritikpunkt ist aber nicht dem p-Wert anzulasten, denn dieses Kriterium ist beliebig, könnte konservativer gesetzt werden und jegliche mechanisierte Entscheidungsmethode kann ausgenutzt werden. Ähnliches kann man zum Thema "P-Hacking" argumentieren [@Head2015, @Wicherts2016]; andere statistische Verfahren können auch gehackt werden.

Ein wichtiger Anklagepunkt lautet, dass der p-Wert nicht nur eine Funktion der Effektgröße ist, sondern auch der Stichprobengröße. Sprich: Bei großen Stichproben wird jede Hypothese signifikant. Damit verliert der p-Wert an Nützlichkeit (vgl. Abb. \@ref(fig:einfluss-pwert). Die Details der Simulation, die hinter Abb. \@ref(fig:einfluss-pwert) sind etwas umfangreicher und hier nicht so wichtig, daher nicht angegeben^[s. hier für Details: https://sebastiansauer.github.io/pvalue_sample_size/].

<div class="figure" style="text-align: center">
<img src="images/einfluss_pwert_crop.pdf" alt="Zwei Haupteinflüsse auf den p-Wert" width="70%" />
<p class="caption">(\#fig:einfluss-pwert)Zwei Haupteinflüsse auf den p-Wert</p>
</div>


Die Verteitigung argumentiert hier, dass das "kein Bug, sondern ein Feature" sei: Wenn man z.B. die Hypothese prüfe, dass der Gewichtsunteschied zwischen Männern und Frauen 0,000000000kg sei und man findet 0,000000123kg Unterschied, ist die getestete Hypothese falsch. Punkt. Der p-Wert gibt demnach das korrekte Ergebnis. Meiner Ansicht nach ist die Antwort zwar richtig, geht aber an den Anforderungen der Praxis vorbei.

### Vertiefung: Praktisches Beispiel zum Stichprobeneinfluss auf den p-Wert

Betrachten wir ein praktisches Beispiel des Einfluss der Stichprobengröße auf den p-Wert. Simulieren wir ein paar Variablen und testen wir, ob sich deren Mittelwerte statistisch signifikant unterscheiden (t-Test). Dabei wählen wir die Werte so, dass sie tatsächlich leicht unterschiedlich sind, dass also die H0 (in unseren Daten) wirklich falsch ist.

Mit steigender Stichprobengröße sollte der Anteil an statistisch signifikanten Tests steigen. Schauen wir, ob dem so ist (vgl. Abb. \@ref(fig:simulate-pvalues)).

<div class="figure" style="text-align: center">
<img src="images/simulate_ps.pdf" alt="Der Anteil an statistisch signifikanten p-Werten bei simulierten Daten. Die X-Achse zeigt die Stichprobengröße (ns), die Y-Achse den Anteil der statistisch signifikanten p-Werte (ps)" width="70%" />
<p class="caption">(\#fig:simulate-pvalues)Der Anteil an statistisch signifikanten p-Werten bei simulierten Daten. Die X-Achse zeigt die Stichprobengröße (ns), die Y-Achse den Anteil der statistisch signifikanten p-Werte (ps)</p>
</div>

Das Diagramm zeigt: Mit steigendem Stichprobenumfang werden die Tests immer signifikanter. Zugespitzt formuliert:

>    Große Stichprobe: Test wird signifikant. Kleine Stichprobe: Test wird nicht signifikant. "Groß" bzw. "klein" heißt hier "groß/klein genug".


## Mythen zum p-Wert

Falsche Lehrmeinungen sterben erst aus, wenn die beteiligten Professoren in Rente gehen, heißt es. Jedenfalls halten sich eine Reihe von Mythen hartnäckig; sie sind alle falsch.


>    Wenn der p-Wert kleiner als 5% ist, dann ist meine Hypothese (H1) sicher richtig.

Richtig ist: "Wenn der p-Wert kleines ist als 5% (oder allgemeiner: kleiner als $\alpha$, dann sind die Daten (oder noch extereme) unwahrscheinlich, vorausgesetzt die H0 gilt".

>    Wenn der p-Wert kleiner als 5% ist, dann habe ich die Ursache eines Phänomens gefunden.

Richtig ist: Keine Statistik kann für sich genommen eine Ursache erkennen. Bestenfalls kann man sagen: hat man alle konkurrierenden Ursachen ausgeschlossen *und* sprechen die Daten für die Ursache *und* sind die Daten eine plausible Erklärung, so erscheint es der beste Schluss, anzunehmen, dass man *eine* Ursache gefunden hat - im Rahmen des Geltungsbereichs einer Studie.

>    Wenn der p-Wert kleiner als 5% ist, dann kann ich meine Studie veröffentlichen.

Richtig. Leider entscheidet zu oft (nur) der p-Wert über das Wohl und Wehe einer Studie. Wichtiger wäre zu prüfen, wie "gut" das Modell ist - wie präzise sind die Vorhersagen? Wie theoretisch befriedigend ist das Modell?


## Zur Philosophie des p-Werts

Der p-Wert basiert auf der Idee, dass man ein Experiment *unendlich* oft wiederholen könnte; und das unter *zufälligen* aber *ansonsten komplett gleichen* Bedingungen.

Ob es im Universum irgendetwas gibt, das unendlich ist, ist umstritten [@ruckerinfinity]. Jedenfalls ist die Vorstellung, das Experiment unendlich oft zu wiederholen, unrealistisch. Inwieweit Zufälligkeit und Vergleichbarkeit hergestellt werden kann, kann auch kritisiert werden [@uncertainty].



## Fazit

Meine Meinung ist, dass der p-Wert ein problematisch ist (und ein Dinosaurier) und nicht oder weniger benutzt werden sollte (das ist eine normative Aussage). Da der p-Wert aber immer noch der Platzhirsch auf vielen Forschungsauen ist, führt kein Weg um ihn herum. Er muss genau verstanden werden: Was er sagt und - wichtiger noch - was er nicht sagt.


<img src="images/meme_pwert_1iw22a_pvalue_dino.jpg" width="30%" style="display: block; margin: auto;" />

