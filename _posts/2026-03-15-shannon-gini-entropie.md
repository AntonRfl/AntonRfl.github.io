---
layout: default
title: "Data Science: Shannon-Entropie und Gini-Unreinheit"
description: "Ein kurzer Einblick in die Mathematik hinter Entscheidungsbäumen."
category: data-science
lang: de
permalink: /data-science/shannon-entropie/
en_url: /en/data-science/shannon-entropy/
---

# Data Science: Shannon-Entropie und Gini-Unreinheit

Das Lernen mit Entscheidungsbäumen ist ein für die Künstliche Intelligenz wichtiges und gleichzeitig einfaches Verfahren. Einer der vielen Vorzüge von Entscheidungsbäumen ist, dass sie sehr wenig Vorbereitung der Daten erfordern. Insbesondere ist keinerlei Skalierung oder Zentrierung von Merkmalen notwendig. Die Grundidee besteht darin, aus Daten Wissen zu erzeugen. In diesem Fall jedoch nicht als Funktion, sondern als Baumgraph. Dies verhilft zu einem größeren Verständnis und einer besseren Interpretierbarkeit der Ergebnisse.

Doch wie entscheidet ein Algorithmus, an welcher Stelle er die Daten am besten aufteilt? Hier kommen zwei zentrale Konzepte ins Spiel: die **Shannon-Entropie** und die **Gini-Unreinheit**.

---

### 1. Die Shannon-Entropie

Einfach gesagt ist die Entropie $H$ ein Maß für die Unsicherheit einer Wahrscheinlichkeitsverteilung. Je größer die Unsicherheit, desto größer die Anzahl der kodierten Bits, die wir benötigen, um die Information darzustellen.

Schauen wir uns bei $n$ Klassen zwei Extremfälle an:
* **Minimale Ungewissheit:** Das Ergebnis tritt sicher auf. Die Verteilung sieht so aus: $\vec{P}=(1,0,0,...,0)$.
* **Maximale Ungewissheit:** Es herrscht absolute Gleichverteilung der Wahrscheinlichkeiten.

#### Die Herleitung der Formel
Um den Informationsgehalt zu messen, stellen wir uns die Frage: Wie viele Bits werden zur Kodierung benötigt?

* Tritt ein Ergebnis sicher auf, benötigen wir 0 Bits.
* Bei einer maximalen Gleichverteilung benötigen wir $x=\log_{2}(n)$ Bits, denn es werden alle $n$ Stellen benötigt. Das ergibt sich aus der Beziehung $2^{x}=n$.

Da bei einer Gleichverteilung die Wahrscheinlichkeit $p$ für jedes Ereignis genau $\frac{1}{n}$ ist, können wir umformen zu $n=\frac{1}{p}$. Setzen wir das in unseren Logarithmus ein, erhalten wir die benötigten Bits für ein einzelnes Ereignis:

$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$

Um nun den allgemeinen Fall für eine ganze Verteilung zu berechnen, bilden wir den Erwartungswert $H$ für die Bitzahl. Dafür gewichten wir die berechneten Bits mit ihrer jeweiligen Auftrittswahrscheinlichkeit $p_{i}$.

Daraus ergibt sich die berühmte Formel der Shannon-Entropie:

$$H(\vec{p})=-\sum_{i=1}^{n}p_i\log_2(p_i)$$

---

### 2. Die Gini-Unreinheit (Gini-Impurity) als Alternative

Neben der Entropie gibt es ein weiteres, sehr populäres Maß, um zu bewerten, wie gut ein Entscheidungsbaum die Daten trennt: Die Gini-Unreinheit.

Während die Entropie nach dem Informationsgehalt fragt, misst die Gini-Unreinheit ganz direkt die "Reinheit" eines Knotens im Baum. Ein Knoten gilt als absolut rein, wenn sämtliche darin enthaltene Datenpunkte der gleichen Kategorie angehören.

Die Formel für die Gini-Unreinheit am $k$-ten Knoten sieht so aus:

$$G_{k}=1-\sum_{i=1}^{n}p_{k,i}^2$$

*(Dabei ist $p_{k,i}$ der Anteil der Datenpunkte der Kategorie $i$ am Knoten $k$.)*

#### Ein kurzes Rechenbeispiel
Stellen wir uns vor, wir werten Daten für einen Skifahrer aus und prüfen das Merkmal "Schnee-Entfernung $\le 100$ km". In einem Zweig dieses Knotens landen exakt 4 "Ja"-Antworten und 0 "Nein"-Antworten. 

Wir berechnen die Gini-Unreinheit für diesen Zweig:

$$G_{\le100}=1-\left(\frac{4}{4}\right)^2-\left(\frac{0}{4}\right)^2=1-1-0=0$$

Eine Gini-Unreinheit von 0 bedeutet: Der Knoten ist perfekt rein! Der Baum hat an dieser Stelle optimal getrennt.

---

### Fazit: Wie nutzen Algorithmen das in der Praxis?

Moderne Machine-Learning-Bibliotheken wie `scikit-learn` in Python verwenden beispielsweise den sogenannten CART-Algorithmus (Classification and Regression Tree), um Entscheidungsbäume zu trainieren. 

Dieser Algorithmus teilt die Trainingsdaten schrittweise so auf, dass die Kostenfunktion minimiert wird. Er sucht also immer nach genau dem Merkmal und dem Schwellenwert, der die reinsten Untermengen erzeugt. Im Falle der Klassifikation minimiert die Kostenfunktion dabei gewichtet die Gini-Unreinheit (oder wahlweise die Entropie) des linken und rechten Zweigs:

$$J(i,t_{i})=\frac{m_{links}}{m}G_{links}+\frac{m_{rechts}}{m}G_{rechts}$$
