---
layout: default
title: "PDFs als Eingriffspunkt: Dokumente mit Python auslesen"
permalink: /pdf-datenquelle/
category: it-security
---

<div class="lang-de" markdown="1">
# PDFs als Eingriffspunkt: Dokumente mit Python auslesen

In der modernen Datenverarbeitung stoßen wir oft auf ein hartnäckiges Problem: Informationen sind in PDFs "gefangen". Ob Verträge, wissenschaftliche Paper oder Rechnungen – das PDF-Format ist perfekt für das menschliche Auge, aber extrem unhandlich für Maschinen.

In diesem Beitrag zeige ich, wie man eine PDF-Datei als Eingriffspunkt für eine Datenpipeline nutzt und den Text mit Python extrahiert.

### Warum ist das so schwer?
Im Gegensatz zu HTML oder reinen Textdateien enthält eine PDF oft keine echte Struktur für Absätze oder Tabellen. Es ist im Grunde nur eine riesige Liste von Anweisungen, wo genau auf einer digitalen "Seite" welcher Buchstabe oder welches Bild gezeichnet werden soll.

### Der Lösungsansatz mit Python
Um eine PDF als Datenquelle zu nutzen, müssen wir sie parsen (einlesen und analysieren). Eine der bekanntesten und einfachsten Bibliotheken dafür in Python ist `PyPDF2` (oder modernere Alternativen wie `pdfplumber`).

Hier ist ein kurzes Beispiel, wie man den Text auslesbar macht:

{% highlight python %}
import PyPDF2

def lese_pdf(dateipfad):
    # Die Datei im Binär-Lese-Modus öffnen
    with open(dateipfad, 'rb') as datei:
        pdf_reader = PyPDF2.PdfReader(datei)
        extrahierter_text = ""
        
        # Durch alle Seiten iterieren und den Text anhängen
        for seite in pdf_reader.pages:
            extrahierter_text += seite.extract_text() + "\n"
            
    return extrahierter_text

# Den Eingriffspunkt testen
mein_text = lese_pdf("mein_dokument.pdf")
print(mein_text[:500]) # Zeigt die ersten 500 Zeichen an
{% endhighlight %}

### Was machen wir mit den Daten?
Sobald wir die PDF in reinen Text (einen String) verwandelt haben, stehen uns alle Türen offen. Dies ist oft der erste Schritt (der Eingriffspunkt) für komplexere Aufgaben:

1. **Information Extraction:** Wir können mit regulären Ausdrücken (RegEx) nach IBANs, Namen oder Beträgen suchen.
2. **Natural Language Processing (NLP):** Wir können den Text analysieren, um Stimmungen zu erkennen oder Zusammenfassungen zu schreiben.
3. **RAG (Retrieval-Augmented Generation):** Wir füttern den Text in eine Vektordatenbank, um ihn später von einem Large Language Model (LLM) wie ChatGPT durchsuchen und beantworten zu lassen.

PDFs zu knacken ist oft der erste und wichtigste Schritt in echten Data-Science-Projekten!
</div>



