# KI-gestützte Dokumentenextraktion und Datenzuordnung

## Take Aways / Management Summary

* Eine verlässliche Extraktion besteht nicht nur aus dem Auslesen von Text: Kontext, ein festes Zielschema und anschließende Prüfungen sind ebenso wichtig.
* Das passende Werkzeug hängt von der Quelle ab: Text-PDFs lassen sich meist direkt parsen, Scans benötigen OCR und Fotos oder komplexe visuelle Inhalte können ein Vision-Modell erfordern.
* Prompts sollten Rolle, Aufgabe und Ausgabeformat klar festlegen. Erlaubte Werte, Fundstellen und ein explizites `null` statt Raten machen Resultate prüfbarer.
* Ein technischer „Harness“ um den Modellaufruf übernimmt Eingangsverarbeitung, Validierung, Routing und Protokollierung. Was sich deterministisch prüfen lässt, sollte Code statt ein weiteres Modell prüfen.
* Unsichere oder ungültige Ergebnisse gehören in einen begrenzten Korrekturkreislauf oder zur menschlichen Prüfung. Ein Modell kann Regelverstöße beheben, aber nicht zuverlässig erkennen, dass ein plausibel wirkender Wert falsch aus dem Dokument gelesen wurde.
* Qualität muss gegen verifizierte Ground-Truth-Daten gemessen werden. Diese Daten ermöglichen Konfidenzschwellen, gezielte Prüfungen und eine iterative Verbesserung von Prompt und Kontext.
* Der Business Case hängt neben der Bearbeitungszeit auch von Fehler- und Compliancekosten, Restprüfaufwand sowie Aufbau und Betrieb der Lösung ab. Fehler werden teurer, je weiter sie in Folgeprozesse gelangen.
* Für Produktzuordnungen empfiehlt sich eine gestufte Logik: eindeutige Codes, bekannte Mappings, semantische Suche und schließlich Regeln/KI mit menschlicher Entscheidung bei unklaren Fällen.

## Inhalt

Die Folien beschreiben einen Ansatz zur automatisierten Extraktion und Zuordnung von Produktdaten aus heterogenen Dokumenten. Ziel sind belastbare, strukturierte Daten für ein PIM-, MDM- oder anderes Zielsystem – nicht bloß erkannter Text.

Zunächst wird die Quelle eingeordnet: Bei strukturierten Daten wie ERP-Exporten, BMECat-Feeds oder CSV stehen Import und Mapping im Vordergrund. Für unstrukturierte Quellen wird je nach Beschaffenheit ein Parser, OCR oder ein Vision-Modell gewählt. Ein Text-PDF mit vorhandener Textebene ist meist der einfachste und günstigste Fall; bei Scans kann OCR die Struktur verlieren, während ein Vision-Modell auch Fotos, Symbole und verteilten visuellen Kontext interpretieren kann.

![Screenshot 2026-10-12 110622.png](<Screenshot 2026-10-12 110622.png>)

Der Extraktionsprozess führt vom Auslesen über das Ergänzen relevanten Kontexts – etwa Verweise, Legenden oder weitere Seiten – bis zur Überführung in ein Zielschema mit Datentypen, Einheiten und erlaubten Wertelisten. Die Folien empfehlen, vor jedem Anwendungsfall Wirtschaftlichkeit, technischen Aufwand der Quelle und den Nachweis der Ergebnisqualität zu klären.

![Screenshot 2026-10-12 110657.png](<Screenshot 2026-10-12 110657.png>)

Ein Extraktions-Prompt wird in drei Blöcke gegliedert: **Rolle** (Perspektive und Arbeitsweise), **Aufgabe** (konkret zu extrahierende Felder und Regeln) und **Ergebnis** (festgelegtes Format, zum Beispiel JSON). Als zentrale Regeln gelten wortgetreue Übernahme, `null` statt eines geratenen Werts, ausschließlich erlaubte Werte, eine nachvollziehbare Fundstelle und Konfidenzangaben. Typische Fehler sind vergessene Einheiten, ungeprüfter Freitext, zu viele Felder auf einmal, zu enge Wertelisten und ein fehlendes Zielschema.

![Screenshot 2026-10-12 110749.png](<Screenshot 2026-10-12 110749.png>) · ![Screenshot 2026-10-12 110921.png](<Screenshot 2026-10-12 110921.png>)

Rund um den Modellaufruf beschreibt die Präsentation einen sechsstufigen Harness: Eingang erkennen und klassifizieren, Quelle und Kontext vorbereiten, das Modell mit Zielschema aufrufen, Ergebnis deterministisch validieren, nach Konfidenz oder Fehlerstatus routen und Fundstellen, Korrekturen sowie Kosten protokollieren. Prüfungen wie Datentypen, Pflichtfelder und Wertelisten sollen im Code erfolgen, wenn sie sich eindeutig formulieren lassen.

![Screenshot 2026-10-12 111054.png](<Screenshot 2026-10-12 111054.png>)

Ein Self-Healing Loop kann erkannte Format- und Regelverstöße als Feedback in einen weiteren Modellaufruf geben. Die Zahl der Versuche muss begrenzt bleiben; danach entscheidet ein Mensch. Der Loop löst jedoch keine zuverlässig falsch gelesenen, aber formal gültigen Werte. Dafür braucht es unter anderem Ground Truth, Konfidenzbetrachtung oder menschliche Prüfung.

![Screenshot 2026-10-12 111245.png](<Screenshot 2026-10-12 111245.png>)

Eine zweite, offline laufende Entwicklungsschleife vergleicht Extraktionen mit geprüften Beispielen, ergänzt den Prompt um beobachtete Fehler und misst erneut. Als Beispiel wird ein Projekt mit 600 Artikelcodes und 95 % Genauigkeit bei Pflichtfeldern genannt; hervorgehoben wird, dass hier Prompt und Kontext angereichert und nicht das Modell nachtrainiert wurden.

![Screenshot 2026-10-12 111540.png](<Screenshot 2026-10-12 111540.png>)

Die vorgestellten Bewertungsraster unterscheiden robuste von komplexen Anwendungsfällen. Bei unterschiedlichen Dokumentarten sind Klassifikation und Zielschema zentrale Aufgaben; bei Katalogen mit Kontext über viele Seiten werden Gesamtüberblick und Zwischenschema wichtiger. Je nach Fall können mehrere Modelle, Bagging und fachliche Prüfungen eingesetzt werden. Verlässliche Daten erfordern ein definiertes Zielschema, messbare Qualitätskontrollen und eine gezielte Prüfung von Einzelfällen.

![Screenshot 2026-10-12 111646.png](<Screenshot 2026-10-12 111646.png>)

Für die menschliche Prüfung werden zwei Ausgangslagen gegenübergestellt: Ohne Ground Truth wird zunächst jedes Ergebnis von Menschen geprüft und freigegebene Werte liefern nach und nach Vergleichsdaten. Mit Ground Truth kann die Qualität je Attribut gemessen werden; Ergebnisse oberhalb einer belegten Schwelle werden automatisiert übernommen, darunter geprüft. Mehrere Modelle können durch Übereinstimmung zusätzliche Hinweise liefern, ersetzen aber keine Prüfung systematischer Fehler. Die vom Modell selbst angegebene Konfidenz ist nicht automatisch kalibriert.

![Screenshot 2026-10-12 111653.png](<Screenshot 2026-10-12 111653.png>)

Der wirtschaftliche Nutzen ergibt sich aus dem heutigen Bearbeitungsaufwand und gebundenen Kosten, ergänzt um Fehler- und Compliancekosten. Davon abzuziehen sind Aufbau und Betrieb der Automatisierung sowie verbleibende Prüfzeit. Eine Amortisationsrechnung sollte daher auch berücksichtigen, wann Fehler entdeckt werden: Ein falscher Wert kann sich über PIM, Folgeprozesse, Katalog und Shop vervielfachen und dort deutlich höhere Kosten verursachen.

![Screenshot 2026-10-12 111718.png](<Screenshot 2026-10-12 111718.png>) · ![Screenshot 2026-10-12 111739.png](<Screenshot 2026-10-12 111739.png>)

Zum Handout gehören ein Colab-Notebook, eine Prompt-Vorlage, eine Methodikübersicht und eine Kurzanleitung. Für die Bewertung eines konkreten Use Cases regen die Folien Fragen zu Volumen und Wiederholung, Dokumenttypen und Layouts, vorhandenen geprüften Altdaten, aktuellem Prüfaufwand, akzeptabler Restfehlerquote und Fähigkeiten des bestehenden PIM an.

![Screenshot 2026-10-12 111804.png](<Screenshot 2026-10-12 111804.png>) · ![Screenshot 2026-10-12 111842.png](<Screenshot 2026-10-12 111842.png>)

Als nächster Schritt wird vorgeschlagen, historische Datensätze gegen eine Ground Truth laufen zu lassen, um sowohl die Maschinenqualität als auch die bisherige manuelle Prozessqualität einzuschätzen.

![Screenshot 2026-10-12 111853.png](<Screenshot 2026-10-12 111853.png>)

Ergänzende Screenshots zeigen eine vierstufige Produktzuordnungslogik: eindeutige Codes, Mappings und Synonyme, semantische Suche sowie Regeln/KI mit menschlicher Entscheidung bei Unsicherheit. Als Datengrundlage werden neun analysierte Projekte, 814 Kundenpositionen, 20 aktive Regelwerke und 13 Produktgruppen genannt.

![Screenshot 2026-10-12 115246.png](<Screenshot 2026-10-12 115246.png>) · ![Screenshot 2026-10-12 115321.png](<Screenshot 2026-10-12 115321.png>) · ![Screenshot 2026-10-12 115428.png](<Screenshot 2026-10-12 115428.png>)
