---
layout: post
title: "Aktivierungsfunktionen: Eine Analyse der Gradienten und Repräsentationen in tiefen Netzen"
description: "Warum tiefe neuronale Netze ohne Nichtlinearität kollabieren: Eine technische Analyse von Vanishing Gradients, ReLU, SiLU, GeLU und der Geometrie von Softmax."
category: data-science
lang: de
permalink: /data-science/aktivierungsfunktionen/
en_url: /en/data-science/activation-functions/
---

# **Eine Analyse der Gradienten und Repräsentationen in tiefen neuronalen Netzen**

Die Entwicklung der künstlichen Intelligenz und insbesondere Deep Learning, ist stark mit den mathematischen Mechanismen verbunden, die es möglich machen, komplexe und nichtlineare Zusammenhänge in Daten zu modellieren. In der Literatur werden oft die Anzahl der Parameter oder die Rechenleistung moderner GPUs im Vordergrund gestellt, aber die eigentliche “Intelligenz” eines neuronalen Netzes liegt in seinen Aktivierungsfunktionen.https://www.google.com/search?q=%C2%A0

Diese Funktionen werden oft als Werkzeuge zum Pressen ("Squashing" von Werten in einem Intervall \[0,1\] betrachtet. Ihre mathematische Rolle ist schützt vor der mathematischen Bedeutungslosigkeit.1https://www.google.com/search?q=%C2%A0 Ohne die Einführung von Nichtlinearität würde selbst ein Modell mit Billionen von Parametern zu einer einfachen, kollabierten linearen Regression degenerieren, die unfähig wäre, selbst einfache logische Probleme wie die XOR-Verknüpfung zu lösen.1

## **Die mathematische Notwendigkeit der Nichtlinearität**

In der linearen Algebra ist die Verknüpfung zweier linearer Abbildungen zwingend wieder eine lineare Abbildung. Mathematisch lässt sich dies durch die Matrixmultiplikation ausdrücken: Wenn eine Schicht eines Netzwerks durch die Transformation $y \= W\_1x \+ b\_1$ und die darauffolgende Schicht durch $z \= W\_2y \+ b\_2$ definiert ist, so ergibt sich die Gesamtabbildung als $z \= W\_2(W\_1x \+ b\_1) \+ b\_2$, was zu $z \= (W\_2W\_1)x \+ (W\_2b\_1 \+ b\_2)$ vereinfacht werden kann.1 Das Produkt zweier Matrizen $W\_2W\_1$ ist dann eine neue Matrix $W\_{eff}$, und die Kombination der Biases ergibt einen neuen Bias-Vektor $b\_{eff}$.https://www.google.com/search?q=%C2%A0

Folglich kollabiert ein beliebig tiefes Netzwerk ohne nichtlineare Aktivierungsfunktionen zu einem flachen Modell, das nur lineare Trennebenen in einem hochdimensionalen Raum definieren kann.1

Die Aktivierungsfunktion $\phi$ bricht diese Kette auf, indem sie zwischen den Schichten eine Funktion einfügt, die nicht additiv oder homogen ist: $z \= \phi(Wx \+ b)$.3 Diese einfache aber geniale Funktion erlaubt es dem Netzwerk, den Eingaberaum so zu krümmen und zu falten, dass komplexe Strukturen – wie Kanten in Bildern, semantische Beziehungen in Texten oder logische Abhängigkeiten – modelliert werden können.1 Die Wahl der spezifischen Funktion $\phi$ entscheidet dabei maßgeblich über die Stabilität der Gradienten während des Trainingsprozesses. Sie bestimmt, ob die Fehlerinformationen über hunderte von Schichten hinweg bis zu den ersten Gewichten zurückfließen können oder ob sie in den "toten Zonen" der Sättigung verschwinden.5

| Konzept | Mathematische Darstellung | Auswirkung ohne Nichtlinearität |
| :---- | :---- | :---- |
| Lineare Schicht | $f(x) \= Wx \+ b$ | Keine Krümmung des Merkmalsraums möglich |
| Komposition | $f(g(x)) \= W\_2(W\_1x \+ b\_1) \+ b\_2$ | Reduktion auf eine einzige Schicht ($W\_{eff}x \+ b\_{eff}$) |
| Repräsentationslimit | $y \= \sum w\_i x\_i$ | Unfähigkeit, XOR oder nichtlineare Manifolds zu lernen |
| Gradientenfluss | $\frac{\partial y}{\partial x} \= W$ | Konstante Gradienten, keine komplexen Anpassungen |

## **Sigmoid und Tanh**https://www.google.com/search?q=%C2%A0

Die Idee: ein biologisches Neuron feuert entweder einen elektrischen Impuls ab oder es bleibt inaktiv. Um dieses Verhalten mathematisch abzubilden, wurden S-förmige Funktionen eingeführt, die einen Übergang zwischen zwei Zuständen simulieren.https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild1 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_1.png\]

### **Die Sigmoid-Funktion und die Gradientensättigung**

Die Sigmoid-Funktion ist definiert als $\sigma(x) \= \frac{1}{1 \+ e^{-x}}$ und ist element der reellen Zahlen auf das Intervall $(0, 1)$ ab.7 Dies war vorteilhaft, da die Ausgaben direkt als Wahrscheinlichkeiten interpretiert werden konnten, was sie zur Standardwahl für die Ausgabeschicht von Binärklassifikationsmodellen machte.9 Ein wesentlicher Nachteil dieser Funktion liegt jedoch in ihrer Ableitung: $\sigma'(x) \= \sigma(x)(1 \- \sigma(x))$. Der Maximalwert dieser Ableitung beträgt 0,25 (bei $x \= 0$).8 Wenn ein Fehlergradient durch ein tiefes Netzwerk mit beispielsweisehttps://www.google.com/search?q=%C2%A0 https://www.google.com/search?q=%C2%A0Schichten zurückpropagiert wird, multiplizieren sich diese Werte. Da jeder Faktor höchstens 0,25 beträgt, schrumpft der Gradient exponentiell ($0,25^{ }$), was dazu führt, dass die Gewichte in den frühen Schichten des Netzwerks nicht mehr aktualisiert werden.11https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
Zudem leiden Sigmoid-Einheiten unter dem Problem der Sättigung. Wenn die Eingabe $x$ sehr große positive oder negative Werte annimmt, nähert sich die Funktion asymptotisch der 1 oder der 0 an, und die Steigung wird extrem flach – sie geht gegen Null.7 In diesem Zustand verliert der Optimierer, da er keine Information mehr darüber erhält, in welche Richtung er die Gewichte anpassen muss, um den Fehler zu minimieren.8https://www.google.com/search?q=%C2%A0 https://www.google.com/search?q=%C2%A0
Ein weiteres Problem ist, dass die Sigmoid-Ausgaben nicht nullzentriert sind. Da alle Ausgaben positiv sind, haben die Gradienten für die Gewichte einer Schicht während der Backpropagation immer das gleiche Vorzeichen, was zu einer ineffizienten Parameteroptimierung führen kann.9

### **Die Hyperbolische Tangens-Funktion (Tanh)**

Um das Problem der fehlenden Nullzentrierung zu beheben, wurde die Tanh-Funktion eingesetzt: $tanh(x) \= \frac{e^x \- e^{-x}}{e^x \+ e^{-x}}$.7 Sie verschiebt den Wertebereich auf $(-1, 1)$, wodurch die durchschnittliche Aktivierung einer Schicht näher bei Null liegt.9 Dies beschleunigt die Konvergenz des Trainingsprozesses im Vergleich zu Sigmoid.8 Dennoch bleibt das fundamentale Problem des Vanishing Gradient Problem bestehen, da Tanh ebenfalls in den äußeren Bereichen sättigt und die Ableitung gegen Null geht.5 Beide Funktionen sind zudem rechenintensiv, da sie die Berechnung von Exponentialfunktionen erfordern, was bei Millionen von Neuronen in jeder Trainingsepoche einen Nachtteil darstellt.14

| Eigenschaft | Sigmoid | Tanh |
| :---- | :---- | :---- |
| Wertebereich | $(0, 1)$ | $(-1, 1)$ |
| Mathematische Form | $(1 \+ e^{-x})^{-1}$ | $\frac{e^{2x} \- 1}{e^{2x} \+ 1}$ |
| Nullzentrierung | Nein | Ja |
| Max. Ableitung | $0,25$ | $1,0$ |
| Sättigung | Bei großen positiven/negativen Werten | Bei großen positiven/negativen Werten |

7

## **Die ReLU-Aktivierungsfunktion**

Der Durchbruch für Deep Learning entstand durch Vereinfachung. Die Rectified Linear Unit (ReLU) ist definiert als $f(x) \= \max(0, x)$. Sie ersetzte sie die komplizierten S-Kurven durch eine teilweise lineare Funktion.9

### **Warum ist ReLU wichtig in Deep Learning**https://www.google.com/search?q=%C2%A0

Ein oft übersehener Vorteil von ReLU ist die Erzeugung von Sparsity. Da alle negativen Eingabewerte exakt auf Null gesetzt werden, sind zu jedem Zeitpunkt viele Neuronen inaktiv.5 In einem biologischen System feuern ebenfalls nie alle Neuronen gleichzeitig. Mathematisch führt diese Sparsity dazu, dass das Netzwerk nur die relevantesten Merkmale für eine bestimmte Eingabe nutzt, was die Effizienz steigert und eine Form der impliziten Regularisierung darstellt, die das Risiko von Overfitting verringern kann.14

### **Nachteil: Exploding Gradients und Dying ReLU**

Ein großes Nachteil von ReLU ist das sie nach oben unbegrenzt sind, d.h. die Aktivierungswerte und damit auch Gradienten können in tiefen Netzen extrem groß werden, was zu numerischer Instabilität und dem Problem der exploding Gradient führt.6 Dies erfordert oft sorgfältige Gewichtsinitialisierungen und Techniken wie Batch-Normalisierung, um die Werte in einem stabilen Bereich zu halten.12https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
Ein größeres Problem ist allerdings das "Dying ReLU"-Problem. Wenn die Gewichte eines Neurons so angepasst werden, dass es für alle Datenpunkte im Trainingsset eine negative Eingabe erhält, gibt es permanent Null aus.12 Da die Ableitung von ReLU für negative Werte Null ist, kann dieses Neuron nie wieder aktualisiert werden, es ist mathematisch "tot" bekannt als Dying ReLU Problem.12 In manchen Fällen können so bis zu 50 % der Neuronen eines Netzwerks während des Trainings inaktiv werden, was die Kapazität des Modells massiv einschränkt.13

## **Überwindung der Toten Zonen: LeakyReLU und ELU**

Um das Problem der "sterbenden" Neuronen der ReLU-Architekturen zubeheben, entwickelten Forscher Varianten, die auch im negativen Bereich einen Informationsfluss erlauben.

### **LeakyReLU und PReLU**

Die LeakyReLU führt eine kleine Steigung $\alpha$ für negative Werte ein: $f(x) \= x$ für $x \> 0$ und $f(x) \= \alpha x$ für $x \le 0$.5 Meist wird $\alpha$ auf 0,01 gesetzt.9https://www.google.com/search?q=%C2%A0 https://www.google.com/search?q=%C2%A0
https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild2 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_2.png\]

Dieser Leak sorgt dafür, dass auch inaktive Neuronen einen minimalen Gradienten behalten und somit nicht komplett absterben sondern durch zukünftige Updates wieder in den aktiven Bereichen wandern können.5https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
Eine Weiterentwicklung ist die Parametric ReLU (PReLU), bei der der Parameter $\alpha$ nicht fest vorgegeben ist, sondern während des Trainings wie ein Gewicht optimiert wird.5 Dies erlaubt es dem Netzwerk, selbst zu entscheiden, wie stark bestimmte Merkmale negativ gewichtet werden.5 Experimente zeigen, dass PReLU oft eine höhere Genauigkeit und schnellere Konvergenz bietet, allerdings auf Kosten einer erhöhten Gefahr des Overfittings, da mehr Parameter gelernt werden müssen.5

### **Exponential Linear Unit (ELU)**

Die Exponential Linear Unit (ELU)ist definiert als: $$f(x) \= \begin{cases} x & \text{if } x \> 0 \\ \alpha(e^x \- 1) & \text{if } x \le 0 \end{cases}$$https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
Im negativen Bereich nähert sich die ELU asymptotisch einem Wert $-\alpha$ an.5 Diese Kurve führt dazu, dass die durchschnittliche Aktivierung einer Schicht näher an Null liegt, ähnlich wie bei Tanh, aber ohne das Vanishing Gradient Problem auf der positiven Seite.13 Durch die exponentielle Sättigung im negativen Bereich ist ELU zudem robuster gegenüber Rauschen und Ausreißern in den Daten.15 Studien belegen, dass Netzwerke mit ELU oft schneller lernen und besser generalisieren als solche mit herkömmlichen ReLUs.13

| Funktion | Verhalten bei x\<0 | Hauptvorteil | Nachteil |
| :---- | :---- | :---- | :---- |
| ReLU | Exakt $0$ | Maximale Sparsity | Permanentes Absterben |
| LeakyReLU | $\alpha x$ (linear) | Verhindert tote Neuronen | Manuelle Wahl von $\alpha$ |
| PReLU | $\alpha\_i x$ (lernbar) | Adaptive Flexibilität | Overfitting-Risiko |
| ELU | $\alpha(e^x \- 1)$ | Glatter Übergang, robust | Rechenintensiv |

5

## **SiLU (Swish) und GeLU**

Mit dem Aufkommen von Big Data und immer tieferen Transformer-Architekturen stießen die einfachen Aktiverungsfunktionen an ihre Grenzen.

### **SiLU (Swish): Entdeckt durch KI**

Die Sigmoid Linear Unit (SiLU) oder Swish-Funktion, wurde von Google-Deepmind mittels Reinforcement Learning und einer Suche über tausende Funktionskombinationen entwickelt.https://www.google.com/search?q=%C2%A0 Sie ist definiert als: $f(x) \= x \cdot \sigma(x) \= \frac{x}{1 \+ e^{-x}}$.4 https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild3 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_3.png\]

Warum ist SiLU besser als ReLU?

1.  Nicht-Monotonie: SiLU besitzt einen kleinen "Dip" unter Null im Bereich von etwa $x \approx \-1,28$, bevor sie asymptotisch gegen Null läuft.4 Das bedeutet, dass sie für kleine negative Werte eine andere Dynamik aufweist als für große negative Werte. Dieses Verhalten hilft dem Netzwerk, komplexere Filterstrukturen zu lernen, die durch eine Nullsetzung verloren gingen. .https://www.google.com/search?q=%C2%A0 https://www.google.com/search?q=%C2%A0
2.  **Smoothness:** Während ReLU am Nullpunkt einen harten Knick besitzt, ist SiLU vollständig differenzierbar. Diese mathematische Glattheit sorgt für eine deutlich stabilere Verlustlandschaft. ReLU neigt dazu, unvorhersehbare Gradientensprünge an scharfen Kanten zu erzeugen. SiLU hingegen glättet diese Bereiche, was die Gradientenberechnung für Optimierungsverfahren wie Adam erheblich stabilisiert.4

https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild 4 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_4.png\]

3.  **Self-Gating:** Die Funktion nutzt ihren eigenen Wert, um den Informationsfluss zu steuern. Der Eingangswert $x$ wird durch seine eigene Sigmoid-Transformation "gegatet".4 Dies ähnelt den Gating-Mechanismen in LSTMs, ist aber mathematisch wesentlich effizienter umsetzbar.4

### **GeLU-Funktion ist Transformer-Standard**

Die Gaussian Error Linear Unit (GeLU) ist die Standardaktivierung in Modellen wie BERT, GPT-2 und den meisten modernen Transformer-Architekturen.13 Sie ist definiert als $f(x) \= x \cdot P(X \le x) \= x \Phi(x)$, wobei $\Phi(x)$ die Verteilungsfunktion der Standardnormalverteilung ist.13https://www.google.com/search?q=%C2%A0 https://www.google.com/search?q=%C2%A0
https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild 5 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_5.png\]

GeLU kombiniert die Eigenschaften von ReLU (Sparsity) mit der Glattheit von SiLU, bietet aber eine probabilistische Interpretation: Ein Wert wird gewichtet damit, wie wahrscheinlich es ist, dass er bei einer Gauß-Verteilung bedeutsam ist.4https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
In der Praxis sind GeLU und SiLU funktional sehr ähnlich. Bei NLP-Aufgaben zeigt GeLU jedoch eine leicht bessere Performance, da diese Funktion die stochastischen Eigenschaften von Sprachdaten präziser abbildet.4 Beide Funktionen erlauben es dem Netzwerk, negative Informationen nicht einfach zu löschen, sondern sie differenziert zu gewichten, was die Repräsentation in extrem tiefen Modellen massiv steigert.4

## **Softmax und die Geometrie der Wahrscheinlichkeit**

Während die beschriebenen Funktionen fast ausschließlich in den Hidden Layers zur Merkmalsextraktion dienen, übernimmt Softmax eine Sonderrolle in dem Output-Layer in Klassifikationen. Softmax ist keine elementweise Funktion, sondern eine multivariate Abbildung und ist definiert als:$$Softmax(z\_i) \= \frac{e^{z\_i}}{\sum\_{j=1}^{K} e^{z\_j}}$$https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
\[Bild 6 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_6.png\]

Sie betrachtet das Verhältnis aller Neuronen der Schicht zueinander und stellt sicher, dass die Summe aller Ausgaben exakt 1 ergibt.11

### ****

\[Bild 7 - Adresse: /assets/images/data-science/aktivierungsfunktionen/Bild\_7png\]

### **Das Problem der Overconfidence**

Ein kritisches Problem moderner tiefer Netze ist die deterministische Overconfidence. Aufgrund ihrer immensen Kapazität und der Verwendung von Funktionen wie ReLU neigen Modelle dazu, eine Klasse extrem zu bevorzugen (z. B. mit einer Wahrscheinlichkeit von 0,999), selbst wenn sie mit Daten konfrontiert werden, die weit außerhalb ihrer Trainingsverteilung (Out-of-Distribution, OOD) liegen.https://www.google.com/search?q=%C2%A0 Diese Pseudogewissheit ist in sicherheitskritischen Bereichen wie der Medizin oder dem autonomen Fahren lebensgefährlich.

### **Temperature Scaling als Korrektiv**

Um die Wahrscheinlichkeitsverteilung zu kalibrieren, wird oft "Temperature Scaling" eingesetzt. Hierbei werden die Logits $z$ vor der Softmax-Berechnung durch einen Skalar $T$ geteilt.https://www.google.com/search?q=%C2%A0 Ein $T \> 1$ erweicht die Verteilung und erhöht die Entropie, was dazu führt, dass das Modell bei Unsicherheit moderatere Wahrscheinlichkeiten ausgibt.https://www.google.com/search?q=%C2%A0 Da $T$ auf einem separaten Validierungsset optimiert wird, bleibt die Genauigkeit erhalten, während die Konfidenzwerte deutlich realistischer werden.https://www.google.com/search?q=%C2%A0

## \*\*Krümmung und Self-Normalizing \*\*https://www.google.com/search?q=%C2%A0

### **Die Bedeutung der zweiten Ableitung**

Die ReLU-Funktion hat eine zweite Ableitung, die fast überall Null ist ($f''(x) \= 0$ für $x \neq 0$). Dies bedeutet, dass der Loss-Landscape eines ReLU-Netzes aus flachen Ebenen besteht, die an scharfen Knicken aufeinanderprallen.https://www.google.com/search?q=%C2%A0 Funktionen wie SiLU oder GeLU hingegen haben eine nicht-triviale Krümmung.13 Dies ist für moderne Optimierer der zweiten Ordnung von unschätzbarem Wert.https://www.google.com/search?q=%C2%A0 Diese Algorithmen nutzen die Information über die lokale Krümmung, um nicht nur die Richtung des Abstiegs, sondern auch die optimale Schrittweite präziser zu berechnen, was die Konvergenz massiv beschleunigen kann.https://www.google.com/search?q=%C2%A0

### **SELU und SNNs**

Die Scaled Exponential Linear Unit (SELU) wurde entwickelt, um "Self-Normalizing Neural Networks" (SNNs) zu ermöglichen.https://www.google.com/search?q=%C2%A0 Durch die Wahl spezifischer Konstanten ($\alpha \approx 1,67326$ und $\lambda \approx 1,0507$) induziert SELU eine Abbildung, die Mittelwert und Varianz der Aktivierungen über hunderte Schichten hinweg stabilisiert.3 Mittels des Banachschen Fixpunktsatzes wurde bewiesen, dass Aktivierungen, die nahe bei Null-Mittelwert und Einheitsvarianz liegen, durch die Schichten hindurch zu genau diesem stabilen Fixpunkt konvergieren.https://www.google.com/search?q=%C2%A0 Dies macht Techniken wie Batch-Normalisierung theoretisch überflüssig und erlaubt das Training extrem tiefer, rein vorwärtsgerichteter Netze ohne Stabilitätsprobleme.https://www.google.com/search?q=%C2%A0

| Mathematisches Konzept | Anwendung | Relevante Funktion |
| :---- | :---- | :---- |
| Erste Ableitung | Backpropagation (Gradient Descent) | Alle (insb. ReLU) |
| Zweite Ableitung | Krümmungsanalyse, Newton-Methoden | SiLU, GeLU, Sine |
| Fixpunkt-Iteration | Automatische Normalisierung | SELU |
| Lipschitz-Stetigkeit | Robustheit gegen Perturbationen | Sigmoid, Tanh |

\#\#https://www.google.com/search?q=%C2%A0

## **Fazit und Ausblick**

Die Wahl der Aktivierungsfunktion ist kein Zufall, sondern folgt klaren architektonischen Standards, die auf der Balance zwischen Recheneffizienz, Gradientenstabilität und Repräsentationskraft basieren.

1.  **Hidden Layers in LLMs/Transformern:** SiLU und GeLU sind der besser in Transformer-Architekturen. Ihre Smothness und Nicht-Monotonie ermöglichen es diesen großen Modellen, extrem subtile Muster in Billionen von Datenpunkten zu erkennen, während gleichzeitig ein stabiler Gradientenfluss gewährleistet bleibt.https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
2.  **CNNs:** ReLU bleibt aufgrund seiner Geschwindigkeit und der natürlichen Sparsity die erste Wahl für mobile Anwendungen und Echtzeit-Bildverarbeitung.14https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
3.  **Klassifikations-Outputs:** Softmax bleibt unverzichtbar, muss aber in modernen Anwendungen durch Techniken wie Temperature Scaling ergänzt werden, um die gefährliche Overconfidence zu begrenzen.https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
4.  **Spezialisierte Netze:** SELU bietet eine Lösung für sehr tiefe MLPs, bei denen man auf komplexe Normalisierungsschichten verzichten möchte.

Die Zukunft der Aktivierungsfunktionen liegt vermutlich in der weiteren Dynamisierung. Neue Ansätze wie "Expanded Gating" (xSiLU, xGELU) experimentieren bereits damit, die Gating-Bereiche während des Trainings individuell für jeden MLP-Block anzupassen. Ohne die ständige Verfeinerung dieser mathematischen Kleinstkomponenten wäre die Revolution der Large Language Models und die Vision einer allgemeinen künstlichen Intelligenz niemals möglich gewesen.

\#\#\#\#https://www.google.com/search?q=%C2%A0

#### **Works cited**

1.  A Fresh Look at Nonlinearity in Deep Learning | Towards Data ..., accessed on April 5, 2026, [https://towardsdatascience.com/a-fresh-look-at-nonlinearity-in-deep-learning-a79b6955d2ad/](https://towardsdatascience.com/a-fresh-look-at-nonlinearity-in-deep-learning-a79b6955d2ad/)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
2.  SiLU Activation Function: Deep Learning Guide - Ultralytics, accessed on April 5, 2026, [https://www.ultralytics.com/glossary/silu-sigmoid-linear-unit](https://www.ultralytics.com/glossary/silu-sigmoid-linear-unit)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
3.  Courage to Learn ML: Tackling Vanishing and Exploding Gradients (Part 2) | Towards Data Science, accessed on April 5, 2026, [https://towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-2-d0b8aed1ce7a/](https://towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-2-d0b8aed1ce7a/)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
4.  Understanding ReLU, LeakyReLU, and PReLU: A Comprehensive Guide | by Juan C Olamendy | Medium, accessed on April 5, 2026, [https://medium.com/@juanc.olamendy/understanding-relu-leakyrelu-and-prelu-a-comprehensive-guide-20f2775d3d64](https://medium.com/@juanc.olamendy/understanding-relu-leakyrelu-and-prelu-a-comprehensive-guide-20f2775d3d64)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
5.  Comparison Of Sigmoid, Tanh And ReLU Activation Functions - AITUDE, accessed on April 5, 2026, [https://www.aitude.com/comparison-of-sigmoid-tanh-and-relu-activation-functions/](https://www.aitude.com/comparison-of-sigmoid-tanh-and-relu-activation-functions/)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
6.  Understanding the Different Types of Activation Functions in Neural Networks - Medium, accessed on April 5, 2026, [https://medium.com/@chaudharyankita667/understanding-the-different-types-of-activation-functions-in-neural-networks-989e439e2018](https://medium.com/@chaudharyankita667/understanding-the-different-types-of-activation-functions-in-neural-networks-989e439e2018)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
7.  Rectified linear unit - Wikipedia, accessed on April 5, 2026, [https://en.wikipedia.org/wiki/Rectified\_linear\_unit](https://en.wikipedia.org/wiki/Rectified_linear_unit)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
8.  ReLU vs ELU: Picking the Right Activation for Deep Nets - DigitalOcean, accessed on April 5, 2026, [https://www.digitalocean.com/community/tutorials/relu-vs-elu-activation-function](https://www.digitalocean.com/community/tutorials/relu-vs-elu-activation-function)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
9.  ReLU vs. LeakyReLU vs. PReLU | Baeldung on Computer Science, accessed on April 5, 2026, [https://www.baeldung.com/cs/relu-vs-leakyrelu-vs-prelu](https://www.baeldung.com/cs/relu-vs-leakyrelu-vs-prelu)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
10. Swish: a Self-Gated Activation Function - ResearchGate, accessed on April 5, 2026, [https://www.researchgate.net/publication/320464797\_Swish\_a\_Self-Gated\_Activation\_Function](https://www.researchgate.net/publication/320464797_Swish_a_Self-Gated_Activation_Function)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
11. On the Disparity Between Swish and GELU | Towards Data Science, accessed on April 5, 2026, [https://towardsdatascience.com/on-the-disparity-between-swish-and-gelu-1ddde902d64b/](https://towardsdatascience.com/on-the-disparity-between-swish-and-gelu-1ddde902d64b/)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
12. What is SwiGLU? Activation Functions Explained - Ultralytics, accessed on April 5, 2026, [https://www.ultralytics.com/glossary/swiglu](https://www.ultralytics.com/glossary/swiglu)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
13. SwiGLU Activation in Transformer Models - Emergent Mind, accessed on April 5, 2026, [https://www.emergentmind.com/topics/swiglu-activation](https://www.emergentmind.com/topics/swiglu-activation)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
14. Exploring the Impact of Temperature Scaling in Softmax for Classification and Adversarial Robustness - arXiv, accessed on April 5, 2026, [https://arxiv.org/html/2502.20604v1](https://arxiv.org/html/2502.20604v1)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
15. What is Out-of-Distribution (OOD) Detection? - Encord, accessed on April 5, 2026, [https://encord.com/blog/what-is-out-of-distribution-ood-detection/](https://encord.com/blog/what-is-out-of-distribution-ood-detection/)https://www.google.com/search?q=%C2%A0https://www.google.com/search?q=%C2%A0
16. Exploring Second Derivative | CodeSignal Learn, accessed on April 5, 2026, [https://codesignal.com/learn/courses/advanced-calculus-for-machine-learning/lessons/exploring-second-derivative](https://codesignal.com/learn/courses/advanced-calculus-for-machine-learning/lessons/exploring-second-derivative)

