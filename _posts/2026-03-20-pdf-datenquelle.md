---
layout: default
title: "PDFs als Eingriffspunkt: Dokumente mit Python auslesen"
permalink: /pdf-datenquelle/
category: data-science
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


<div class="lang-en" markdown="1">
# Using PDFs as an Entry Point: Parsing Documents with Python

In modern data processing, we often run into a stubborn problem: information is "trapped" in PDFs. Whether contracts, scientific papers, or invoices – the PDF format is perfect for the human eye, but extremely clunky for machines.

In this post, I will show you how to use a PDF file as an entry point for a data pipeline and extract the text using Python.

### Why is this so difficult?
Unlike HTML or plain text files, a PDF often does not contain a real structure for paragraphs or tables. It is basically just a huge list of instructions indicating exactly where on a digital "page" which letter or image should be drawn.

### The Python Approach
To use a PDF as a data source, we have to parse it (read and analyze it). One of the most well-known and simplest libraries for this in Python is `PyPDF2` (or more modern alternatives like `pdfplumber`).

Here is a short example of how to make the text readable:

{% highlight python %}
import PyPDF2

def read_pdf(file_path):
    # Open the file in binary read mode
    with open(file_path, 'rb') as file:
        pdf_reader = PyPDF2.PdfReader(file)
        extracted_text = ""
        
        # Iterate through all pages and append the text
        for page in pdf_reader.pages:
            extracted_text += page.extract_text() + "\n"
            
    return extracted_text

# Test the entry point
my_text = read_pdf("my_document.pdf")
print(my_text[:500]) # Shows the first 500 characters
{% endhighlight %}

### What do we do with the data?
Once we have transformed the PDF into plain text (a string), all doors are open to us. This is often the first step (the entry point) for more complex tasks:

1. **Information Extraction:** We can use regular expressions (RegEx) to search for IBANs, names, or amounts.
2. **Natural Language Processing (NLP):** We can analyze the text to detect sentiment or write summaries.
3. **RAG (Retrieval-Augmented Generation):** We feed the text into a vector database so that it can later be searched and answered by a Large Language Model (LLM) like ChatGPT.

Cracking PDFs is often the first and most important step in real-world Data Science projects!
</div>
