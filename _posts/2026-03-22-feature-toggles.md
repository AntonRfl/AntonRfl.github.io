---
layout: default
title: "Feature Toggles: Parallele Entwicklung ohne Merge-Hölle"
permalink: /feature-toggles/
category: software-engineering
---

<div class="lang-de" markdown="1">
# Feature Toggles: Parallele Entwicklung ohne Merge-Hölle

[cite_start]Traditionell steuert man parallele Entwicklung in der Softwareentwicklung durch Branching[cite: 454]. [cite_start]Dies hat jedoch Nachteile: Langlebige Branches führen oft zu schweren Merge-Konflikten (der sogenannten "Merge Hell") [cite: 455] [cite_start]und der Code ist erst integriert, wenn der Branch gemerged wird (verzögerte Integration)[cite: 456].

Die elegante Lösung für dieses Problem sind **Feature Toggles**. [cite_start]Sie reduzieren den Bedarf an langfristigen Branches und verringern so Merge-Konflikte enorm[cite: 458, 459]. [cite_start]Anstatt in separaten Zweigen zu arbeiten, können mehrere Entwickler gleichzeitig an verschiedenen Features im selben Code-Branch arbeiten, isoliert nur durch diese Schalter[cite: 462, 463]. [cite_start]Dadurch sind Feature Toggles die technische Voraussetzung für moderne Praktiken wie Continuous Integration und Trunk-Based Development[cite: 460, 461].

### Wie funktionieren Feature Toggles?
[cite_start]Im Kontext des Configuration Managements (CM) wirken Feature Toggles wie „konfigurierbare CIs“ (Configuration Items)[cite: 469]. [cite_start]Das Verhalten der Software wird nicht mehr allein durch die Dateiversion bestimmt, sondern maßgeblich durch die Einstellung dieser Schalter[cite: 470]. [cite_start]Änderungen am produktiven System erfolgen viel schneller durch das Umschalten einer Konfiguration, anstatt durch aufwendige Code-Merges[cite: 471].

[cite_start]Hier ist ein einfaches Architektur-Beispiel in Java[cite: 474]:

{% highlight java %}
public class PaymentService {
    public void processPayment(Payment payment) {
        // Der "Schalter" entscheidet zur Laufzeit
        if (FeatureToggles.isEnabled("new-payment-flow")) {
            handlePaymentNew(payment);
        } else {
            handlePaymentLegacy(payment);
        }
    }
}
{% endhighlight %}

[cite_start]Dieses Beispiel illustriert eine logische statt einer physischen Trennung: Der neue Code (`handlePaymentNew`) liegt direkt neben dem alten Code im Hauptstamm[cite: 475]. [cite_start]Die `if`-Abfrage fungiert als Schalter, der zur Laufzeit entscheidet, welcher Weg eingeschlagen wird[cite: 476]. 

### "Dark Launching" und Arten von Toggles
[cite_start]Ein riesiger Vorteil dieses Ansatzes ist das sogenannte "Dark Launching": Unfertige oder neue Funktionen können sicher auf das Produktionssystem deployed werden, da sie für den Endanwender durch den Schalter noch unsichtbar ("dunkel") geschaltet sind[cite: 466, 467]. [cite_start]Features lassen sich im laufenden Betrieb an- oder abschalten, ohne dass eine neue Software-Version (Baseline) erstellt oder installiert werden muss[cite: 464, 465].

[cite_start]Es gibt verschiedene Arten von Feature Toggles, je nach Einsatzzweck[cite: 480]:
* [cite_start]**Release Toggles**: Blenden unfertige Features aus, sind meist kurzlebig und sollten nach dem Release entfernt werden[cite: 481, 482, 483].
* [cite_start]**Operations Toggles**: Aktivieren Notfallmodi oder Workarounds und sind eine extrem wichtige Komponente im Incident-Management[cite: 484, 485, 486].
* [cite_start]**Experiment Toggles**: Werden für A/B-Testing und die Variantensteuerung genutzt[cite: 487, 488].
* [cite_start]**Permission Toggles**: Steuern die Feature-Auslieferung für bestimmte Rollen oder Nutzergruppen und sind oft langlebig[cite: 490, 491, 492].

[cite_start]**Ein wichtiger Hinweis für die Praxis:** Die Zustände der Toggles (An/Aus) müssen zwingend Teil der CM-Dokumentation und der Logs werden, um den Systemzustand später exakt rekonstruieren zu können[cite: 472, 473].
</div>


<div class="lang-en" markdown="1">
# Feature Toggles: Parallel Development without Merge Hell

[cite_start]Traditionally, parallel development in software engineering is managed through branching[cite: 454]. [cite_start]However, this has disadvantages: Long-lived branches often lead to severe merge conflicts (the so-called "Merge Hell") [cite: 455][cite_start], and the code is only integrated once the branch is merged (delayed integration)[cite: 456].

The elegant solution to this problem is **Feature Toggles**. [cite_start]They reduce the need for long-term branches and thus enormously decrease merge conflicts[cite: 458, 459]. [cite_start]Instead of working in separate branches, multiple developers can simultaneously work on different features in the same code branch, isolated only by these switches[cite: 462, 463]. [cite_start]This makes feature toggles the technical prerequisite for modern practices like Continuous Integration and Trunk-Based Development[cite: 460, 461].

### How do Feature Toggles work?
[cite_start]In the context of Configuration Management (CM), feature toggles act like "configurable CIs" (Configuration Items)[cite: 469]. [cite_start]The behavior of the software is no longer determined solely by the file version, but significantly by the setting of these switches[cite: 470]. [cite_start]Changes to the production system happen much faster by simply switching a configuration, rather than through complex code merges[cite: 471].

[cite_start]Here is a simple architectural example in Java[cite: 474]:

{% highlight java %}
public class PaymentService {
    public void processPayment(Payment payment) {
        // The "switch" decides at runtime
        if (FeatureToggles.isEnabled("new-payment-flow")) {
            handlePaymentNew(payment);
        } else {
            handlePaymentLegacy(payment);
        }
    }
}
{% endhighlight %}

[cite_start]This example illustrates a logical instead of a physical separation: The new code (`handlePaymentNew`) sits directly next to the old code in the main trunk[cite: 475]. [cite_start]The `if` statement acts as a switch that decides at runtime which path is taken[cite: 476].

### "Dark Launching" and Types of Toggles
[cite_start]A massive advantage of this approach is so-called "Dark Launching": Unfinished or new features can be safely deployed to the production system because they are still set to be invisible ("dark") to the end user via the switch[cite: 466, 467]. [cite_start]Features can be turned on or off during operation without having to create or install a new software version (baseline)[cite: 464, 465].

[cite_start]There are different types of feature toggles depending on their intended use[cite: 480]:
* [cite_start]**Release Toggles**: Hide unfinished features, are mostly short-lived, and should be removed after the release[cite: 481, 482, 483].
* [cite_start]**Operations Toggles**: Activate emergency modes or workarounds and are an extremely important component in incident management[cite: 484, 485, 486].
* [cite_start]**Experiment Toggles**: Are used for A/B testing and variant control[cite: 487, 488].
* [cite_start]**Permission Toggles**: Control feature delivery for specific roles or user groups and are often long-lived[cite: 490, 491, 492].

[cite_start]**An important note for practice:** The states of the toggles (On/Off) must absolutely become part of the CM documentation and logs in order to be able to exactly reconstruct the system state later[cite: 472, 473].
</div>
