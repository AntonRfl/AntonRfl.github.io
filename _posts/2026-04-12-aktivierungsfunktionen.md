---
layout: default
title: "Aktivierungsfunktionen: Eine Analyse der Gradienten und Repräsentationen in tiefen Netzen"
description: "Warum tiefe neuronale Netze ohne Nichtlinearität kollabieren: Eine technische Analyse von Vanishing Gradients, ReLU, SiLU, GeLU und der Geometrie von Softmax."
category: data-science
lang: de
permalink: /data-science/aktivierungsfunktionen/
en_url: /en/data-science/activation-functions/
---

# Aktivierungsfunktionen: Eine Analyse der Gradienten und Repräsentationen in tiefen neuronalen Netzen

Die Entwicklung der künstlichen Intelligenz und insbesondere Deep Learning ist stark mit den mathematischen Mechanismen verbunden, die es möglich machen, komplexe und nichtlineare Zusammenhänge in Daten zu modellieren. In der Literatur werden oft die Anzahl der Parameter oder die Rechenleistung moderner GPUs in den Vordergrund gestellt, aber die eigentliche "Intelligenz" eines neuronalen Netzes liegt in seinen **Aktivierungsfunktionen**.

Diese Funktionen werden oft als Werkzeuge zum "Squashing" von Werten in einem Intervall $[0,1]$ betrachtet. Ihre mathematische Rolle schützt jedoch vor mathematischer Bedeutungslosigkeit. Ohne die Einführung von Nichtlinearität würde selbst ein Modell mit Billionen von Parametern zu einer einfachen, kollabierten linearen Regression degenerieren — unfähig, selbst einfache logische Probleme wie die XOR-Verknüpfung zu lösen.

---

### Die mathematische Notwendigkeit der Nichtlinearität

In der linearen Algebra ist die Verknüpfung zweier linearer Abbildungen zwingend wieder eine lineare Abbildung. Mathematisch lässt sich dies durch die Matrixmultiplikation ausdrücken: Wenn eine Schicht eines Netzwerks durch die Transformation $y = W_1x + b_1$ und die darauffolgende Schicht durch $z = W_2y + b_2$ definiert ist, so ergibt sich die Gesamtabbildung als:

$$z = W_2(W_1x + b_1) + b_2 = (W_2W_1)x + (W_2b_1 + b_2)$$

Das Produkt zweier Matrizen $W_2W_1$ ist eine neue Matrix $W_{eff}$, und die Kombination der Biases ergibt einen neuen Bias-Vektor $b_{eff}$. Folglich kollabiert ein beliebig tiefes Netzwerk ohne nichtlineare Aktivierungsfunktionen zu einem flachen Modell, das nur lineare Trennebenen in einem hochdimensionalen Raum definieren kann.

Die Aktivierungsfunktion $\phi$ bricht diese Kette auf, indem sie zwischen den Schichten eine Funktion einfügt, die nicht additiv oder homogen ist:

$$z = \phi(Wx + b)$$

Diese einfache aber geniale Funktion erlaubt es dem Netzwerk, den Eingaberaum so zu krümmen und zu falten, dass komplexe Strukturen — wie Kanten in Bildern, semantische Beziehungen in Texten oder logische Abhängigkeiten — modelliert werden können. Die Wahl der spezifischen Funktion $\phi$ entscheidet dabei maßgeblich über die Stabilität der Gradienten während des Trainingsprozesses.

| Konzept | Mathematische Darstellung | Auswirkung ohne Nichtlinearität |
| :--- | :--- | :--- |
| Lineare Schicht | $f(x) = Wx + b$ | Keine Krümmung des Merkmalsraums möglich |
| Komposition | $f(g(x)) = W_2(W_1x + b_1) + b_2$ | Reduktion auf eine einzige Schicht |
| Repräsentationslimit | $y = \sum w_i x_i$ | Unfähigkeit, XOR oder nichtlineare Manifolds zu lernen |
| Gradientenfluss | $\frac{\partial y}{\partial x} = W$ | Konstante Gradienten, keine komplexen Anpassungen |

---

### Sigmoid und Tanh

Die Idee: Ein biologisches Neuron feuert entweder einen elektrischen Impuls ab oder es bleibt inaktiv. Um dieses Verhalten mathematisch abzubilden, wurden S-förmige Funktionen eingeführt, die einen Übergang zwischen zwei Zuständen simulieren.

![Sigmoid und Tanh Aktivierungsfunktionen](/assets/images/data-science/aktivierungsfunktion/Bild_1.PNG)

### Die Sigmoid-Funktion und die Gradientensättigung

Die Sigmoid-Funktion ist definiert als:

$$\sigma(x) = \frac{1}{1 + e^{-x}}$$

Sie bildet die reellen Zahlen auf das Intervall $(0, 1)$ ab. Dies war vorteilhaft, da die Ausgaben direkt als Wahrscheinlichkeiten interpretiert werden konnten, was sie zur Standardwahl für die Ausgabeschicht von Binärklassifikationsmodellen machte. Ein wesentlicher Nachteil liegt jedoch in ihrer Ableitung:

$$\sigma'(x) = \sigma(x)(1 - \sigma(x))$$

Der Maximalwert dieser Ableitung beträgt $0{,}25$ (bei $x = 0$). Wenn ein Fehlergradient durch ein tiefes Netzwerk mit beispielsweise $n$ Schichten zurückpropagiert wird, multiplizieren sich diese Werte. Da jeder Faktor höchstens $0{,}25$ beträgt, schrumpft der Gradient exponentiell ($0{,}25^n$), was dazu führt, dass die Gewichte in den frühen Schichten des Netzwerks nicht mehr aktualisiert werden — das **Vanishing Gradient Problem**.

Zudem leiden Sigmoid-Einheiten unter dem Problem der **Sättigung**. Wenn die Eingabe $x$ sehr große positive oder negative Werte annimmt, nähert sich die Funktion asymptotisch der $1$ oder der $0$ an, und die Steigung wird extrem flach. In diesem Zustand verliert der Optimierer jede Richtungsinformation.

Ein weiteres Problem ist, dass die Sigmoid-Ausgaben **nicht nullzentriert** sind. Da alle Ausgaben positiv sind, haben die Gradienten für die Gewichte einer Schicht während der Backpropagation immer das gleiche Vorzeichen, was zu einer ineffizienten Parameteroptimierung führen kann.

### Die Hyperbolische Tangens-Funktion (Tanh)

Um das Problem der fehlenden Nullzentrierung zu beheben, wurde die Tanh-Funktion eingesetzt:

$$\tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$

Sie verschiebt den Wertebereich auf $(-1, 1)$, wodurch die durchschnittliche Aktivierung einer Schicht näher bei Null liegt. Dies beschleunigt die Konvergenz des Trainingsprozesses im Vergleich zu Sigmoid. Dennoch bleibt das fundamentale Problem des **Vanishing Gradient** bestehen, da Tanh ebenfalls in den äußeren Bereichen sättigt. Beide Funktionen sind zudem rechenintensiv, da sie die Berechnung von Exponentialfunktionen erfordern.

| Eigenschaft | Sigmoid | Tanh |
| :--- | :--- | :--- |
| Wertebereich | $(0, 1)$ | $(-1, 1)$ |
| Mathematische Form | $(1 + e^{-x})^{-1}$ | $\frac{e^{2x} - 1}{e^{2x} + 1}$ |
| Nullzentrierung | Nein | Ja |
| Max. Ableitung | $0{,}25$ | $1{,}0$ |
| Sättigung | Bei großen positiven/negativen Werten | Bei großen positiven/negativen Werten |

---

### Die ReLU-Aktivierungsfunktion

Der Durchbruch für Deep Learning entstand durch Vereinfachung. Die **Rectified Linear Unit (ReLU)** ist definiert als:

$$f(x) = \max(0, x)$$

Sie ersetzte die komplizierten S-Kurven durch eine teilweise lineare Funktion.

### Warum ist ReLU wichtig in Deep Learning?

Ein oft übersehener Vorteil von ReLU ist die Erzeugung von **Sparsity**. Da alle negativen Eingabewerte exakt auf Null gesetzt werden, sind zu jedem Zeitpunkt viele Neuronen inaktiv. Mathematisch führt diese Sparsity dazu, dass das Netzwerk nur die relevantesten Merkmale für eine bestimmte Eingabe nutzt, was die Effizienz steigert und eine Form der impliziten Regularisierung darstellt, die das Risiko von Overfitting verringern kann.

### Nachteil: Exploding Gradients und Dying ReLU

Ein großer Nachteil von ReLU ist, dass sie nach oben unbegrenzt ist. Die Aktivierungswerte und damit auch Gradienten können in tiefen Netzen extrem groß werden, was zu numerischer Instabilität und dem **Exploding Gradient Problem** führt. Dies erfordert oft sorgfältige Gewichtsinitialisierungen und Techniken wie Batch-Normalisierung.

Ein größeres Problem ist das **"Dying ReLU"-Problem**. Wenn die Gewichte eines Neurons so angepasst werden, dass es für alle Datenpunkte im Trainingsset eine negative Eingabe erhält, gibt es permanent Null aus. Da die Ableitung von ReLU für negative Werte Null ist, kann dieses Neuron nie wieder aktualisiert werden — es ist mathematisch "tot". In manchen Fällen können so bis zu 50 % der Neuronen eines Netzwerks während des Trainings inaktiv werden, was die Kapazität des Modells massiv einschränkt.

---

### Überwindung der Toten Zonen: LeakyReLU und ELU

Um das Problem der "sterbenden" Neuronen zu beheben, entwickelten Forscher Varianten, die auch im negativen Bereich einen Informationsfluss erlauben.

### LeakyReLU und PReLU

Die **LeakyReLU** führt eine kleine Steigung $\alpha$ für negative Werte ein:

$$f(x) = \begin{cases} x & \text{if } x > 0 \\ \alpha x & \text{if } x \le 0 \end{cases}$$

Meist wird $\alpha$ auf $0{,}01$ gesetzt.

![LeakyReLU Aktivierungsfunktion](/assets/images/data-science/aktivierungsfunktion/Bild_2.PNG)

Dieser "Leak" sorgt dafür, dass auch inaktive Neuronen einen minimalen Gradienten behalten und somit nicht komplett absterben, sondern durch zukünftige Updates wieder in den aktiven Bereich wandern können.

Eine Weiterentwicklung ist die **Parametric ReLU (PReLU)**, bei der der Parameter $\alpha$ nicht fest vorgegeben ist, sondern während des Trainings wie ein Gewicht optimiert wird. Dies erlaubt es dem Netzwerk, selbst zu entscheiden, wie stark bestimmte Merkmale negativ gewichtet werden. Experimente zeigen, dass PReLU oft eine höhere Genauigkeit und schnellere Konvergenz bietet, allerdings auf Kosten einer erhöhten Gefahr des Overfittings.

### Exponential Linear Unit (ELU)

Die **Exponential Linear Unit (ELU)** ist definiert als:

$$f(x) = \begin{cases} x & \text{if } x > 0 \\ \alpha(e^x - 1) & \text{if } x \le 0 \end{cases}$$

Im negativen Bereich nähert sich die ELU asymptotisch einem Wert $-\alpha$ an. Diese Kurve führt dazu, dass die durchschnittliche Aktivierung einer Schicht näher an Null liegt, ähnlich wie bei Tanh, aber ohne das Vanishing Gradient Problem auf der positiven Seite. Durch die exponentielle Sättigung im negativen Bereich ist ELU zudem robuster gegenüber Rauschen und Ausreißern in den Daten. Studien belegen, dass Netzwerke mit ELU oft schneller lernen und besser generalisieren als solche mit herkömmlichen ReLUs.

| Funktion | Verhalten bei $x < 0$ | Hauptvorteil | Nachteil |
| :--- | :--- | :--- | :--- |
| ReLU | Exakt $0$ | Maximale Sparsity | Permanentes Absterben |
| LeakyReLU | $\alpha x$ (linear) | Verhindert tote Neuronen | Manuelle Wahl von $\alpha$ |
| PReLU | $\alpha_i x$ (lernbar) | Adaptive Flexibilität | Overfitting-Risiko |
| ELU | $\alpha(e^x - 1)$ | Glatter Übergang, robust | Rechenintensiv |

---

### SiLU (Swish) und GeLU

Mit dem Aufkommen von Big Data und immer tieferen Transformer-Architekturen stießen die einfachen Aktivierungsfunktionen an ihre Grenzen.

### SiLU (Swish): Entdeckt durch KI

Die **Sigmoid Linear Unit (SiLU)** oder Swish-Funktion wurde von Google DeepMind mittels Reinforcement Learning und einer Suche über tausende Funktionskombinationen entwickelt. Sie ist definiert als:

$$f(x) = x \cdot \sigma(x) = \frac{x}{1 + e^{-x}}$$

![SiLU (Swish) Aktivierungsfunktion](/assets/images/data-science/aktivierungsfunktion/Bild_3.PNG)

**Warum ist SiLU besser als ReLU?**

1. **Nicht-Monotonie:** SiLU besitzt einen kleinen "Dip" unter Null im Bereich von etwa $x \approx -1{,}28$, bevor sie asymptotisch gegen Null läuft. Das bedeutet, dass sie für kleine negative Werte eine andere Dynamik aufweist als für große negative Werte. Dieses Verhalten hilft dem Netzwerk, komplexere Filterstrukturen zu lernen.

2. **Smoothness:** Während ReLU am Nullpunkt einen harten Knick besitzt, ist SiLU vollständig differenzierbar. Diese mathematische Glattheit sorgt für eine deutlich stabilere Verlustlandschaft. SiLU glättet diese Bereiche, was die Gradientenberechnung für Optimierungsverfahren wie Adam erheblich stabilisiert.

![Vergleich ReLU vs SiLU Verlustlandschaft](/assets/images/data-science/aktivierungsfunktion/Bild_4.PNG)

3. **Self-Gating:** Die Funktion nutzt ihren eigenen Wert, um den Informationsfluss zu steuern. Der Eingangswert $x$ wird durch seine eigene Sigmoid-Transformation "gegatet". Dies ähnelt den Gating-Mechanismen in LSTMs, ist aber mathematisch wesentlich effizienter umsetzbar.

### GeLU: Der Transformer-Standard

Die **Gaussian Error Linear Unit (GeLU)** ist die Standardaktivierung in Modellen wie BERT, GPT-2 und den meisten modernen Transformer-Architekturen. Sie ist definiert als:

$$f(x) = x \cdot P(X \le x) = x \cdot \Phi(x)$$

wobei $\Phi(x)$ die Verteilungsfunktion der Standardnormalverteilung ist.

![GeLU Aktivierungsfunktion](/assets/images/data-science/aktivierungsfunktion/Bild_5.PNG)

GeLU kombiniert die Eigenschaften von ReLU (Sparsity) mit der Glattheit von SiLU, bietet aber eine probabilistische Interpretation: Ein Wert wird gewichtet damit, wie wahrscheinlich es ist, dass er bei einer Gauß-Verteilung bedeutsam ist.

In der Praxis sind GeLU und SiLU funktional sehr ähnlich. Bei NLP-Aufgaben zeigt GeLU jedoch eine leicht bessere Performance, da diese Funktion die stochastischen Eigenschaften von Sprachdaten präziser abbildet. Beide Funktionen erlauben es dem Netzwerk, negative Informationen nicht einfach zu löschen, sondern sie differenziert zu gewichten.

---

### Softmax und die Geometrie der Wahrscheinlichkeit

Während die beschriebenen Funktionen fast ausschließlich in den Hidden Layers zur Merkmalsextraktion dienen, übernimmt **Softmax** eine Sonderrolle im Output-Layer bei Klassifikationsaufgaben. Softmax ist keine elementweise Funktion, sondern eine multivariate Abbildung:

$$\text{Softmax}(z_i) = \frac{e^{z_i}}{\sum_{j=1}^{K} e^{z_j}}$$

Sie betrachtet das Verhältnis aller Neuronen der Schicht zueinander und stellt sicher, dass die Summe aller Ausgaben exakt $1$ ergibt.

![Softmax Visualisierung](/assets/images/data-science/aktivierungsfunktion/Bild_6.PNG)

![Softmax Geometrie](/assets/images/data-science/aktivierungsfunktion/Bild_7.PNG)

### Das Problem der Overconfidence

Ein kritisches Problem moderner tiefer Netze ist die **deterministische Overconfidence**. Aufgrund ihrer immensen Kapazität und der Verwendung von Funktionen wie ReLU neigen Modelle dazu, eine Klasse extrem zu bevorzugen (z. B. mit einer Wahrscheinlichkeit von $0{,}999$), selbst wenn sie mit Daten konfrontiert werden, die weit außerhalb ihrer Trainingsverteilung (Out-of-Distribution, OOD) liegen. Diese Pseudogewissheit ist in sicherheitskritischen Bereichen wie der Medizin oder dem autonomen Fahren lebensgefährlich.

### Temperature Scaling als Korrektiv

Um die Wahrscheinlichkeitsverteilung zu kalibrieren, wird oft **Temperature Scaling** eingesetzt. Hierbei werden die Logits $z$ vor der Softmax-Berechnung durch einen Skalar $T$ geteilt:

$$\text{Softmax}_T(z_i) = \frac{e^{z_i / T}}{\sum_{j=1}^{K} e^{z_j / T}}$$

Ein $T > 1$ erweicht die Verteilung und erhöht die Entropie, was dazu führt, dass das Modell bei Unsicherheit moderatere Wahrscheinlichkeiten ausgibt. Da $T$ auf einem separaten Validierungsset optimiert wird, bleibt die Genauigkeit erhalten, während die Konfidenzwerte deutlich realistischer werden.

---

### Krümmung und Self-Normalizing Networks

### Die Bedeutung der zweiten Ableitung

Die ReLU-Funktion hat eine zweite Ableitung, die fast überall Null ist ($f''(x) = 0$ für $x \neq 0$). Dies bedeutet, dass die Loss-Landscape eines ReLU-Netzes aus flachen Ebenen besteht, die an scharfen Knicken aufeinanderprallen. Funktionen wie SiLU oder GeLU hingegen haben eine nicht-triviale Krümmung. Dies ist für moderne Optimierer der zweiten Ordnung von unschätzbarem Wert, da diese Algorithmen die Information über die lokale Krümmung nutzen, um nicht nur die Richtung des Abstiegs, sondern auch die optimale Schrittweite präziser zu berechnen.

### SELU und Self-Normalizing Neural Networks

Die **Scaled Exponential Linear Unit (SELU)** wurde entwickelt, um "Self-Normalizing Neural Networks" (SNNs) zu ermöglichen. Durch die Wahl spezifischer Konstanten ($\alpha \approx 1{,}67326$ und $\lambda \approx 1{,}0507$) induziert SELU eine Abbildung, die Mittelwert und Varianz der Aktivierungen über hunderte Schichten hinweg stabilisiert. Mittels des **Banachschen Fixpunktsatzes** wurde bewiesen, dass Aktivierungen, die nahe bei Null-Mittelwert und Einheitsvarianz liegen, durch die Schichten hindurch zu genau diesem stabilen Fixpunkt konvergieren. Dies macht Techniken wie Batch-Normalisierung theoretisch überflüssig.

| Mathematisches Konzept | Anwendung | Relevante Funktion |
| :--- | :--- | :--- |
| Erste Ableitung | Backpropagation (Gradient Descent) | Alle (insb. ReLU) |
| Zweite Ableitung | Krümmungsanalyse, Newton-Methoden | SiLU, GeLU |
| Fixpunkt-Iteration | Automatische Normalisierung | SELU |
| Lipschitz-Stetigkeit | Robustheit gegen Perturbationen | Sigmoid, Tanh |

---

### Fazit und Ausblick

Die Wahl der Aktivierungsfunktion ist kein Zufall, sondern folgt klaren architektonischen Standards, die auf der Balance zwischen Recheneffizienz, Gradientenstabilität und Repräsentationskraft basieren.

1. **Hidden Layers in LLMs/Transformern:** SiLU und GeLU sind der Standard in Transformer-Architekturen. Ihre Smoothness und Nicht-Monotonie ermöglichen es diesen großen Modellen, extrem subtile Muster in Billionen von Datenpunkten zu erkennen, während gleichzeitig ein stabiler Gradientenfluss gewährleistet bleibt.

2. **CNNs:** ReLU bleibt aufgrund seiner Geschwindigkeit und der natürlichen Sparsity die erste Wahl für mobile Anwendungen und Echtzeit-Bildverarbeitung.

3. **Klassifikations-Outputs:** Softmax bleibt unverzichtbar, muss aber in modernen Anwendungen durch Techniken wie Temperature Scaling ergänzt werden, um die gefährliche Overconfidence zu begrenzen.

4. **Spezialisierte Netze:** SELU bietet eine Lösung für sehr tiefe MLPs, bei denen man auf komplexe Normalisierungsschichten verzichten möchte.

Die Zukunft der Aktivierungsfunktionen liegt vermutlich in der weiteren Dynamisierung. Neue Ansätze wie "Expanded Gating" (xSiLU, xGELU) experimentieren bereits damit, die Gating-Bereiche während des Trainings individuell für jeden MLP-Block anzupassen. Ohne die ständige Verfeinerung dieser mathematischen Kleinstkomponenten wäre die Revolution der Large Language Models und die Vision einer allgemeinen künstlichen Intelligenz niemals möglich gewesen.
