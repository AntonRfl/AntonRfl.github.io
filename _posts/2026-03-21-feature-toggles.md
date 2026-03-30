---
layout: default
title: "Feature Toggles: Parallele Entwicklung ohne Merge-Hölle"
description: "Wie man Feature Flags richtig einsetzt, um Code-Konflikte zu vermeiden und Releases sicher zu steuern."
category: software-engineering
lang: de
permalink: /software-engineering/feature-toggles/
en_url: /en/software-engineering/feature-toggles/
---

# Feature Toggles: Parallele Entwicklung ohne Merge-Hölle

Traditionell steuert man parallele Entwicklung in der Softwareentwicklung durch Branching. Dies hat jedoch Nachteile: Langlebige Branches führen oft zu schweren Merge-Konflikten (der sogenannten "Merge Hell") und der Code ist erst integriert, wenn der Branch gemerged wird (verzögerte Integration).

Die elegante Lösung für dieses Problem sind **Feature Toggles**. Sie reduzieren den Bedarf an langfristigen Branches und verringern so Merge-Konflikte enorm. Anstatt in separaten Zweigen zu arbeiten, können mehrere Entwickler gleichzeitig an verschiedenen Features im selben Code-Branch arbeiten, isoliert nur durch diese Schalter. Dadurch sind Feature Toggles die technische Voraussetzung für moderne Praktiken wie Continuous Integration und Trunk-Based Development.

### Wie funktionieren Feature Toggles?
Im Kontext des Configuration Managements (CM) wirken Feature Toggles wie „konfigurierbare CIs“ (Configuration Items). Das Verhalten der Software wird nicht mehr allein durch die Dateiversion bestimmt, sondern maßgeblich durch die Einstellung dieser Schalter. Änderungen am produktiven System erfolgen viel schneller durch das Umschalten einer Konfiguration, anstatt durch aufwendige Code-Merges.

Hier ist ein einfaches Architektur-Beispiel in Java:

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

Dieses Beispiel illustriert eine logische statt einer physischen Trennung: Der neue Code (`handlePaymentNew`) liegt direkt neben dem alten Code im Hauptstamm. Die `if`-Abfrage fungiert als Schalter, der zur Laufzeit entscheidet, welcher Weg eingeschlagen wird. 

### "Dark Launching" und Arten von Toggles
Ein riesiger Vorteil dieses Ansatzes ist das sogenannte "Dark Launching": Unfertige oder neue Funktionen können sicher auf das Produktionssystem deployed werden, da sie für den Endanwender durch den Schalter noch unsichtbar ("dunkel") geschaltet sind. Features lassen sich im laufenden Betrieb an- oder abschalten, ohne dass eine neue Software-Version (Baseline) erstellt oder installiert werden muss.

Es gibt verschiedene Arten von Feature Toggles, je nach Einsatzzweck:
* **Release Toggles**: Blenden unfertige Features aus, sind meist kurzlebig und sollten nach dem Release entfernt werden.
* **Operations Toggles**: Aktivieren Notfallmodi oder Workarounds und sind eine extrem wichtige Komponente im Incident-Management.
* **Experiment Toggles**: Werden für A/B-Testing und die Variantensteuerung genutzt.
* **Permission Toggles**: Steuern die Feature-Auslieferung für bestimmte Rollen oder Nutzergruppen und sind oft langlebig.

**Ein wichtiger Hinweis für die Praxis:** Die Zustände der Toggles (An/Aus) müssen zwingend Teil der CM-Dokumentation und der Logs werden, um den Systemzustand später exakt rekonstruieren zu können.
