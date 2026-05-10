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

Traditionell steuert man parallele Entwicklung in der Softwareentwicklung durch Branching. Dies hat jedoch Nachteile: Langlebige Branches führen oft zu schweren Merge-Konflikten "Merge Hell" und der Code ist erst integriert, wenn der Branch gemerged wird (verzögerte Integration).

Die elegante Lösung für dieses Problem sind **Feature Toggles**. Sie reduzieren den Bedarf an langfristigen Branches und verringern somit Merge-Konflikte enorm. Anstatt in separaten Zweigen zu arbeiten, können mehrere Entwickler gleichzeitig an verschiedenen Features im selben Code-Branch arbeiten, isoliert nur durch diese Schalter. Dadurch sind Feature Toggles die technische Voraussetzung für moderne Praktiken wie Continuous Integration und Trunk-Based Development.

### Wie funktionieren Feature Toggles?
Feature Toggles (oder manchmal Feature Switches genannt) sind eine Technik in der Softwareentwicklung, mit der Funktionen einer Anwendung zur Laufzeit aktiviert oder deaktiviert werden können, ohne den Code neu deployen zu müssen. Sie fungieren als "If-Statements", die entscheiden, ob ein neuer Code für Nutzer sichtbar ist. Im Kontext des Configuration Managements (CM) wirken Feature Toggles wie „konfigurierbare CIs“ (Configuration Items). Änderungen am produktiven System erfolgen viel schneller durch das Umschalten einer Konfiguration, anstatt durch aufwendige Code-Merges.

**Vorteile**:
* Neue Funktionen können im Live-System getestet werden, ohne sie für alle Nutzer freizuschalten "Dark Launching"
* Code kann in die Produktion übernommen werden, während er noch in der Entwicklung ist, was zu kontinuierlicher Integration (CI/CD) beiträgt.
* Features können bei Fehlern sofort "ausgeschaltet" werden, statt eine Rücknahme des Deployments durchzuführen.

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

Dieses Beispiel zeigt eine logische statt einer physischen Trennung: Der neue Code (`handlePaymentNew`) liegt direkt neben dem alten Code im Hauptstamm. Die `if`-Abfrage fungiert als Schalter, der zur Laufzeit entscheidet, welcher Weg eingeschlagen wird. 

### "Dark Launching" und Arten von Toggles
Ein besonderes Highlight dieser Technik ist wie oben erwähnt "Dark Launching": Unfertige oder neue Funktionen können sicher in der Produktionsumgebung deployed werden, da sie für den Endanwender durch den Schalter noch unsichtbar ("dunkel") geschaltet sind. Features lassen sich im laufenden Betrieb an- oder abschalten, ohne dass eine neue Software-Version (Baseline) erstellt oder installiert werden muss.

Es gibt neben den Feature Toggles auch andere verschiedene Arten, je nach Einsatzzweck:
* **Release Toggles**: Blenden unfertige Features aus, sind meist kurzlebig und sollten nach dem Release entfernt werden.
* **Operations Toggles**: Aktivieren Notfallmodi oder Workarounds und sind eine extrem wichtige Komponente im Incident-Management.
* **Experiment Toggles**: Werden für A/B-Testing und die Variantensteuerung genutzt.
* **Permission Toggles**: Steuern die Feature-Auslieferung für bestimmte Rollen oder Nutzergruppen und sind oft langlebig.

**Ein wichtiger Hinweis für die Praxis:** Die Zustände der Toggles (An/Aus) müssen zwingend Teil der CM-Dokumentation und der Logs werden, um den Systemzustand später exakt rekonstruieren zu können.
