---
layout: default
title: "Data Science: Shannon-Entropie und Gini-Unreinheit"
title_en: "Data Science: Shannon Entropy and Gini Impurity"
description: "Ein kurzer Einblick in die Mathematik hinter Entscheidungsbäumen."
description_en: "A brief look into the mathematics behind decision trees."
category: data-science
---

<div class="lang-de" markdown="1">
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
</div>


<div class="lang-en" markdown="1">
# Data Science: Shannon Entropy and Gini Impurity

Learning with decision trees is an important and simultaneously simple method in Artificial Intelligence. One of the many advantages of decision trees is that they require very little data preparation. In particular, no scaling or centering of features is necessary. The basic idea is to generate knowledge from data. In this case, however, not as a function, but as a tree graph. This helps to achieve greater understanding and better interpretability of the results.

But how does an algorithm decide where to best split the data? This is where two central concepts come into play: **Shannon Entropy** and **Gini Impurity**.

---

### 1. Shannon Entropy

Simply put, the entropy $H$ is a measure of the uncertainty of a probability distribution. The greater the uncertainty, the greater the number of encoded bits we need to represent the information.

Let's look at two extreme cases for $n$ classes:
* **Minimal uncertainty:** The result occurs with certainty. The distribution looks like this: $\vec{P}=(1,0,0,...,0)$.
* **Maximal uncertainty:** There is an absolute uniform distribution of probabilities.

#### Derivation of the formula
To measure the information content, we ask ourselves: How many bits are needed for encoding?

* If a result occurs with certainty, we need 0 bits.
* With a maximal uniform distribution, we need $x=\log_{2}(n)$ bits, because all $n$ places are needed. This results from the relationship $2^{x}=n$.

Since with a uniform distribution the probability $p$ for each event is exactly $\frac{1}{n}$, we can rearrange this to $n=\frac{1}{p}$. Substituting this into our logarithm, we get the required bits for a single event:

$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$

To calculate the general case for an entire distribution, we form the expected value $H$ for the number of bits. To do this, we weight the calculated bits with their respective probability of occurrence $p_{i}$.

This results in the famous formula for Shannon Entropy:

$$H(\vec{p})=-\sum_{i=1}^{n}p_i\log_2(p_i)$$

---

### 2. Gini Impurity as an Alternative

Besides entropy, there is another very popular measure to evaluate how well a decision tree splits the data: Gini Impurity.

While entropy asks about the information content, Gini Impurity directly measures the "purity" of a node in the tree. A node is considered absolutely pure if all data points contained in it belong to the same category.

The formula for the Gini Impurity at the $k$-th node looks like this:

$$G_{k}=1-\sum_{i=1}^{n}p_{k,i}^2$$

*(Where $p_{k,i}$ is the proportion of data points of category $i$ at node $k$.)*

#### A short calculation example
Imagine we are evaluating data for a skier and checking the feature "Snow Distance $\le 100$ km". In one branch of this node, exactly 4 "Yes" answers and 0 "No" answers end up. 

We calculate the Gini Impurity for this branch:

$$G_{\le100}=1-\left(\frac{4}{4}\right)^2-\left(\frac{0}{4}\right)^2=1-1-0=0$$

A Gini Impurity of 0 means: The node is perfectly pure! The tree has split optimally at this point.

---

### Conclusion: How do algorithms use this in practice?

Modern machine learning libraries like `scikit-learn` in Python use, for example, the so-called CART algorithm (Classification and Regression Tree) to train decision trees. 

This algorithm splits the training data step by step so that the cost function is minimized. It therefore always looks for exactly the feature and the threshold value that generates the purest subsets. In the case of classification, the cost function minimizes the weighted Gini Impurity (or alternatively the entropy) of the left and right branch:

$$J(i,t_{i})=\frac{m_{left}}{m}G_{left}+\frac{m_{right}}{m}G_{right}$$
</div>---
layout: default
title: "Data Science: Shannon-Entropie und Gini-Unreinheit"
permalink: /shannon-gini-entropie/
---

<div class="lang-de" markdown="1">
# Data Science Basics: Shannon-Entropie und Gini-Unreinheit

Das Lernen mit Entscheidungsbäumen ist ein für die Künstliche Intelligenz wichtiges und gleichzeitig einfaches Verfahren[cite: 2]. [cite_start]Einer der vielen Vorzüge von Entscheidungsbäumen ist, dass sie sehr wenig Vorbereitung der Daten erfordern[cite: 3]. [cite_start]Insbesondere ist keinerlei Skalierung oder Zentrierung von Merkmalen notwendig[cite: 4]. [cite_start]Die Grundidee besteht darin, aus Daten Wissen zu erzeugen[cite: 5]. [cite_start]In diesem Fall jedoch nicht als Funktion, sondern als Baumgraph[cite: 5]. [cite_start]Dies verhilft zu einem größeren Verständnis und einer besseren Interpretierbarkeit der Ergebnisse[cite: 6].

Doch wie entscheidet ein Algorithmus, an welcher Stelle er die Daten am besten aufteilt? Hier kommen zwei zentrale Konzepte ins Spiel: die **Shannon-Entropie** und die **Gini-Unreinheit**.

---

### 1. Die Shannon-Entropie

[cite_start]Einfach gesagt ist die Entropie $H$ ein Maß für die Unsicherheit einer Wahrscheinlichkeitsverteilung[cite: 223]. Je größer die Unsicherheit, desto größer die Anzahl der kodierten Bits, die wir benötigen, um die Information darzustellen.

Schauen wir uns bei $n$ Klassen zwei Extremfälle an:
* [cite_start]**Minimale Ungewissheit:** Das Ergebnis tritt sicher auf[cite: 180, 181]. [cite_start]Die Verteilung sieht so aus: $\vec{P}=(1,0,0,...,0)$[cite: 180].
* [cite_start]**Maximale Ungewissheit:** Es herrscht absolute Gleichverteilung der Wahrscheinlichkeiten[cite: 183, 184].

#### Die Herleitung der Formel
[cite_start]Um den Informationsgehalt zu messen, stellen wir uns die Frage: Wie viele Bits werden zur Kodierung benötigt? [cite: 185, 186]

* [cite_start]Tritt ein Ergebnis sicher auf, benötigen wir 0 Bits[cite: 188].
* [cite_start]Bei einer maximalen Gleichverteilung benötigen wir $x=\log_{2}(n)$ Bits, denn es werden alle $n$ Stellen benötigt[cite: 189]. [cite_start]Das ergibt sich aus der Beziehung $2^{x}=n$[cite: 190].

[cite_start]Da bei einer Gleichverteilung die Wahrscheinlichkeit $p$ für jedes Ereignis genau $\frac{1}{n}$ ist, können wir umformen zu $n=\frac{1}{p}$[cite: 191]. Setzen wir das in unseren Logarithmus ein, erhalten wir die benötigten Bits für ein einzelnes Ereignis:

[cite_start]$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$ [cite: 192]

[cite_start]Um nun den allgemeinen Fall für eine ganze Verteilung zu berechnen, bilden wir den Erwartungswert $H$ für die Bitzahl[cite: 193]. [cite_start]Dafür gewichten wir die berechneten Bits mit ihrer jeweiligen Auftrittswahrscheinlichkeit $p_{i}$[cite: 195].

Daraus ergibt sich die berühmte Formel der Shannon-Entropie:

[cite_start]$$H(\vec{p})=-\sum_{i=1}^{n}p_i\log_2(p_i)$$ [cite: 224]

---

### 2. Die Gini-Unreinheit (Gini-Impurity) als Alternative

Neben der Entropie gibt es ein weiteres, sehr populäres Maß, um zu bewerten, wie gut ein Entscheidungsbaum die Daten trennt: Die Gini-Unreinheit.

[cite_start]Während die Entropie nach dem Informationsgehalt fragt, misst die Gini-Unreinheit ganz direkt die "Reinheit" eines Knotens im Baum[cite: 232]. [cite_start]Ein Knoten gilt als absolut rein, wenn sämtliche darin enthaltene Datenpunkte der gleichen Kategorie angehören[cite: 232].

Die Formel für die Gini-Unreinheit am $k$-ten Knoten sieht so aus:

[cite_start]$$G_{k}=1-\sum_{i=1}^{n}p_{k,i}^2$$ [cite: 234, 235]

[cite_start]*(Dabei ist $p_{k,i}$ der Anteil der Datenpunkte der Kategorie $i$ am Knoten $k$.)* [cite: 236]

#### Ein kurzes Rechenbeispiel
Stellen wir uns vor, wir werten Daten für einen Skifahrer aus und prüfen das Merkmal "Schnee-Entfernung $\le 100$ km". In einem Zweig dieses Knotens landen exakt 4 "Ja"-Antworten und 0 "Nein"-Antworten[cite: 240, 241, 254]. 

Wir berechnen die Gini-Unreinheit für diesen Zweig:

$$G_{\le100}=1-\left(\frac{4}{4}\right)^2-\left(\frac{0}{4}\right)^2=1-1-0=0$$ [cite: 240, 241, 242]

Eine Gini-Unreinheit von 0 bedeutet: Der Knoten ist perfekt rein! Der Baum hat an dieser Stelle optimal getrennt.

---

### Fazit: Wie nutzen Algorithmen das in der Praxis?

Moderne Machine-Learning-Bibliotheken wie `scikit-learn` in Python verwenden beispielsweise den sogenannten CART-Algorithmus (Classification and Regression Tree), um Entscheidungsbäume zu trainieren[cite: 258]. 

Dieser Algorithmus teilt die Trainingsdaten schrittweise so auf, dass die Kostenfunktion minimiert wird[cite: 259, 262]. Er sucht also immer nach genau dem Merkmal und dem Schwellenwert, der die reinsten Untermengen erzeugt[cite: 260]. Im Falle der Klassifikation minimiert die Kostenfunktion dabei gewichtet die Gini-Unreinheit (oder wahlweise die Entropie) des linken und rechten Zweigs:

$$J(i,t_{i})=\frac{m_{links}}{m}G_{links}+\frac{m_{rechts}}{m}G_{rechts}$$ [cite: 263]
</div>


<div class="lang-en" markdown="1">
# Data Science Basics: Shannon Entropy and Gini Impurity

Learning with decision trees is an important and simultaneously simple method in Artificial Intelligence[cite: 2]. One of the many advantages of decision trees is that they require very little data preparation[cite: 3]. In particular, no scaling or centering of features is necessary[cite: 4]. The basic idea is to generate knowledge from data[cite: 5]. In this case, however, not as a function, but as a tree graph[cite: 5]. This helps to achieve greater understanding and better interpretability of the results[cite: 6].

But how does an algorithm decide where to best split the data? This is where two central concepts come into play: **Shannon Entropy** and **Gini Impurity**.

---

### 1. Shannon Entropy

Simply put, the entropy $H$ is a measure of the uncertainty of a probability distribution[cite: 223]. The greater the uncertainty, the greater the number of encoded bits we need to represent the information.

Let's look at two extreme cases for $n$ classes:
* [cite_start]**Minimal uncertainty:** The result occurs with certainty[cite: 180, 181]. [cite_start]The distribution looks like this: $\vec{P}=(1,0,0,...,0)$[cite: 180].
* [cite_start]**Maximal uncertainty:** There is an absolute uniform distribution of probabilities[cite: 183, 184].

#### Derivation of the formula
[cite_start]To measure the information content, we ask ourselves: How many bits are needed for encoding? [cite: 185, 186]

* [cite_start]If a result occurs with certainty, we need 0 bits[cite: 188].
* [cite_start]With a maximal uniform distribution, we need $x=\log_{2}(n)$ bits, because all $n$ places are needed[cite: 189]. [cite_start]This results from the relationship $2^{x}=n$[cite: 190].

[cite_start]Since with a uniform distribution the probability $p$ for each event is exactly $\frac{1}{n}$, we can rearrange this to $n=\frac{1}{p}$[cite: 191]. Substituting this into our logarithm, we get the required bits for a single event:

[cite_start]$$x=\log_{2}\left(\frac{1}{p}\right)=-\log_{2}(p)$$ [cite: 192]

[cite_start]To calculate the general case for an entire distribution, we form the expected value $H$ for the number of bits[cite: 193]. [cite_start]To do this, we weight the calculated bits with their respective probability of occurrence $p_{i}$[cite: 195].

This results in the famous formula for Shannon Entropy:

[cite_start]$$H(\vec{p})=-\sum_{i=1}^{n}p_i\log_2(p_i)$$ [cite: 224]

---

### 2. Gini Impurity as an Alternative

Besides entropy, there is another very popular measure to evaluate how well a decision tree splits the data: Gini Impurity.

[cite_start]While entropy asks about the information content, Gini Impurity directly measures the "purity" of a node in the tree[cite: 232]. [cite_start]A node is considered absolutely pure if all data points contained in it belong to the same category[cite: 232].

The formula for the Gini Impurity at the $k$-th node looks like this:

[cite_start]$$G_{k}=1-\sum_{i=1}^{n}p_{k,i}^2$$ [cite: 234, 235]

[cite_start]*(Where $p_{k,i}$ is the proportion of data points of category $i$ at node $k$.)* [cite: 236]

#### A short calculation example
Imagine we are evaluating data for a skier and checking the feature "Snow Distance $\le 100$ km". In one branch of this node, exactly 4 "Yes" answers and 0 "No" answers end up[cite: 240, 241, 254]. 

We calculate the Gini Impurity for this branch:

$$G_{\le100}=1-\left(\frac{4}{4}\right)^2-\left(\frac{0}{4}\right)^2=1-1-0=0$$ [cite: 240, 241, 242]

A Gini Impurity of 0 means: The node is perfectly pure! The tree has split optimally at this point.

---

### Conclusion: How do algorithms use this in practice?

Modern machine learning libraries like `scikit-learn` in Python use, for example, the so-called CART algorithm (Classification and Regression Tree) to train decision trees[cite: 258]. 

This algorithm splits the training data step by step so that the cost function is minimized[cite: 259, 262]. It therefore always looks for exactly the feature and the threshold value that generates the purest subsets[cite: 260]. In the case of classification, the cost function minimizes the weighted Gini Impurity (or alternatively the entropy) of the left and right branch:

$$J(i,t_{i})=\frac{m_{left}}{m}G_{left}+\frac{m_{right}}{m}G_{right}$$ [cite: 263]
</div>
