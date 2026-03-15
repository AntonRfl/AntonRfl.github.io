---
layout: default
title: "Shannon-Entropie und Gini-Unreinheit in Entscheidungsbäumen"
permalink: /shannon-gini-entropie/
---

# Data Science Basics: Shannon-Entropie und Gini-Unreinheit

[cite_start]Entscheidungsbäume sind ein für die Künstliche Intelligenz wichtiges und gleichzeitig einfaches Verfahren[cite: 2]. [cite_start]Einer ihrer großen Vorzüge ist, dass sie sehr wenig Vorbereitung der Daten erfordern – insbesondere ist keinerlei Skalierung oder Zentrierung von Merkmalen notwendig[cite: 3, 4]. [cite_start]Die Grundidee besteht darin, aus Daten Wissen zu erzeugen und dieses anschaulich als Baumgraph darzustellen[cite: 5].

Doch wie entscheidet ein Algorithmus, an welcher Stelle er die Daten am besten aufteilt? Hier kommen zwei zentrale Konzepte ins Spiel: die **Shannon-Entropie** und die **Gini-Unreinheit**.

---

### 1. Die Shannon-Entropie

[cite_start]Einfach gesagt ist die Entropie $H$ ein Maß für die Unsicherheit einer Wahrscheinlichkeitsverteilung[cite: 223]. [cite_start]Je größer die Unsicherheit, desto größer die Anzahl der kodierten Bits, die wir benötigen, um die Information darzustellen[cite: 203].

Schauen wir uns bei $n$ Klassen zwei Extremfälle an:
* [cite_start]**Minimale Ungewissheit:** Das Ergebnis tritt sicher auf[cite: 180, 182]. [cite_start]Die Verteilung sieht so aus: $\vec{P}=(1,0,0,...,0)$[cite: 180].
* [cite_start]**Maximale Ungewissheit:** Es herrscht absolute Gleichverteilung der Wahrscheinlichkeiten[cite: 183, 184].

#### Die Herleitung der Formel
[cite_start]Um den Informationsgehalt zu messen, stellen wir uns die Frage: Wie viele Bits werden zur Kodierung benötigt? [cite: 185, 186]

* [cite_start]Tritt ein Ergebnis sicher auf, benötigen wir **0 Bits**[cite: 188].
* [cite_start]Bei einer maximalen Gleichverteilung benötigen wir $x=\log_{2}(n)$ Bits, denn es werden alle $n$ Stellen benötigt[cite: 189]. [cite_start]Das ergibt sich aus der Beziehung $2^{x}=n$[cite: 190].

[cite_start]Da bei einer Gleichverteilung die Wahrscheinlichkeit $p$ für jedes Ereignis genau $\frac{1}{n}$ ist, können wir umformen zu $n=\frac{1}{p}$[cite: 191]. Setzen wir das in unseren Logarithmus ein, erhalten wir die benötigten Bits für ein einzelnes Ereignis:
[cite_start]$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$ [cite: 192]

[cite_start]Um nun den allgemeinen Fall für eine ganze Verteilung zu berechnen, bilden wir den Erwartungswert $H$ für die Bitzahl[cite: 193]. [cite_start]Dafür gewichten wir die berechneten Bits mit ihrer jeweiligen Auftrittswahrscheinlichkeit $p_{i}$[cite: 195].

Daraus ergibt sich die berühmte Formel der Shannon-Entropie:
[cite_start]$$H(\vec{p}) = -\sum_{i=1}^{n} p_i \log_2(p_i)$$ [cite: 224]



---

### 2. Die Gini-Unreinheit (Gini-Impurity) als Alternative

[cite_start]Neben der Entropie gibt es ein weiteres, sehr populäres Maß, um zu bewerten, wie gut ein Entscheidungsbaum die Daten trennt: Die Gini-Unreinheit[cite: 232].

[cite_start]Während die Entropie nach dem Informationsgehalt fragt, misst die Gini-Unreinheit ganz direkt die "Reinheit" eines Knotens im Baum[cite: 232]. [cite_start]Ein Knoten gilt als absolut rein, wenn sämtliche darin enthaltene Datenpunkte der gleichen Kategorie angehören[cite: 232].

Die Formel für die Gini-Unreinheit am $k$-ten Knoten sieht so aus:
[cite_start]$$G_{k} = 1 - \sum_{i=1}^{n} p_{k,i}^2$$ [cite: 234, 235]
[cite_start]*(Dabei ist $p_{k,i}$ der Anteil der Datenpunkte der Kategorie $i$ am Knoten $k$[cite: 236].)*

#### Ein kurzes Rechenbeispiel
Stellen wir uns vor, wir werten Daten für einen Skifahrer aus und prüfen das Merkmal "Schnee-Entfernung $\le 100$ km"[cite: 239, 243]. In einem Zweig dieses Knotens landen exakt 4 "Ja"-Antworten und 0 "Nein"-Antworten[cite: 240, 246]. 

Wir berechnen die Gini-Unreinheit für diesen Zweig:
$$G_{\le 100} = 1 - \left(\frac{4}{4}\right)^2 - \left(\frac{0}{4}\right)^2 = 1 - 1 - 0 = 0$$ [cite: 239-243]

Eine Gini-Unreinheit von 0 bedeutet: Der Knoten ist perfekt rein! Der Baum hat an dieser Stelle optimal getrennt[cite: 232, 242].

---

### Fazit: Wie nutzen Algorithmen das in der Praxis?

Moderne Machine-Learning-Bibliotheken wie `scikit-learn` in Python verwenden beispielsweise den sogenannten CART-Algorithmus (Classification and Regression Tree), um Entscheidungsbäume zu trainieren[cite: 258]. 

Dieser Algorithmus teilt die Trainingsdaten schrittweise so auf, dass die Kostenfunktion minimiert wird[cite: 259, 262]. Er sucht also immer nach genau dem Merkmal und dem Schwellenwert, der die reinsten Untermengen erzeugt[cite: 260]. Im Falle der Klassifikation minimiert die Kostenfunktion dabei gewichtet die Gini-Unreinheit (oder wahlweise die Entropie) des linken und rechten Zweigs:
$$J(i,t_{i}) = \frac{m_{links}}{m} G_{links} + \frac{m_{rechts}}{m} G_{rechts}$$ [cite: 263]
