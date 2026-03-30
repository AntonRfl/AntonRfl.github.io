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


