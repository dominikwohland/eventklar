# SPEC.md · Kleist Events

HTW Berlin · Smart Applications · Gruppe 3 Eventplanung  
Kundin: Sabine Kleist, Kleist Events

**Quelle:** Meeting 1, zwölf Fragen und Antworten. `M1-F01` bis `M1-F12` bezeichnen ihre Reihenfolge. Diese Fassung wurde am 7. November 2026 nachträglich aus den Antworten rekonstruiert. Sie enthält keine Erkenntnisse aus Meeting 2.

**Ziel:** Die bisher auf Excel, Outlook, E-Mails und Notizbuch verteilten Informationen zu Kunden, Events, Dienstleisterbuchungen, Kosten und Aufgaben zusammenführen (M1-F01, F06, F12).

## Entitäten

Die fett markierten IDs sind eindeutige Schlüssel. Pro Entität werden drei bis fünf Attribute einschließlich Schlüssel angegeben. Beziehungen stehen im nächsten Abschnitt; technische Fremdschlüssel sind nicht zusätzlich aufgeführt.

| Entität | Attribute |
|---|---|
| Kunde | **kunde_id**, name, email, telefon |
| Event | **event_id**, bezeichnung, eventdatum, gesamtplanbudget, locationangabe |
| Dienstleister | **dienstleister_id**, name, kontaktangabe, erfahrungsstatus (Stamm, Neu), kontaktart (regulär, privat) |
| Gewerk | **gewerk_id**, bezeichnung, beschreibung |
| Buchung | **buchung_id**, vereinbarter_preis, buchungsstatus, zahlungsfrist, schriftliche_zusage_am |
| Aufgabe | **aufgabe_id**, titel, faelligkeit, bearbeitungsstatus, zustaendigkeit |
| Gewerkbudget | **gewerkbudget_id**, planbetrag, planungsnotiz |
| Reservierungsnachweis | **reservierungsnachweis_id**, auftragsbestaetigung_am, erforderliche_anzahlung, eingegangene_anzahlung |

**Interviewgrundlage:** Kunde/Event: M1-F01, F10, F11; Dienstleister/Buchung/Gewerk: M1-F02, F03, F06; Gewerkeplanung: M1-F04; Aufgaben: M1-F12; Reservierung: M1-F04, F07.

**Modellierungsannahmen:** Eigene IDs, Gewerkbudget und Reservierungsnachweis als separate Entitäten; Location und Zuständigkeit zunächst als Text. Stamm/Neu und regulär/privat sind unabhängige Merkmale. Die Zahlungsübersicht ersetzt keine Buchhaltung.

## Beziehungen

| Nr. | Beziehung | Kardinalität und Bedeutung |
|---|---|---|
| B1 | Kunde — Event | **1 : 0..n**; jedes Event hat genau einen Kunden, ein Kunde kann mehrere Events haben. |
| B2 | Event — Buchung | **1 : 0..n**; jede Buchung gehört genau einem Event. |
| B3 | Dienstleister — Buchung | **1 : 0..n**; jede Buchung betrifft genau einen Dienstleister. |
| B4 | Gewerk — Buchung | **1 : 0..n**; jede Buchung wird genau einem Gewerk zugeordnet. |
| B5 | Event — Aufgabe | **1 : 0..n**; jede Aufgabe gehört genau einem Event. |
| B6 | Event — Gewerkbudget | **1 : 0..n**; jeder Planansatz gehört genau einem Event. |
| B7 | Gewerk — Gewerkbudget | **1 : 0..n**; jeder Planansatz betrifft genau ein Gewerk. |
| B8 | Event — Reservierungsnachweis | **1 : 0..1**; ein Event hat höchstens einen aktuellen Nachweis, ein Nachweis gehört genau einem Event. |

**n:m:** Event ↔ Dienstleister über Buchung (B2/B3). Mehrere Anbieter wirken an einem Event mit; ein Anbieter kann für mehrere Events gebucht sein (M1-F02, F10). Auch Event ↔ Gewerk ist für Planansätze über Gewerkbudget n:m. Diese Erläuterungen werden nicht zusätzlich zu B1–B8 gezählt.

**Modellierungsannahmen:** Genau ein Auftraggeber pro Event; ein Gewerk pro Buchung; höchstens ein Planansatz je Event/Gewerk. Die BuchungID ist eigenständig. Ob derselbe Anbieter mehrere Gewerke desselben Events übernimmt, wurde noch nicht ausdrücklich besprochen.

## Business Rules

- **BR1 – Dienstleisterauswahl:** Bei vergleichbarem Preis und vergleichbarer Qualität bevorzugt Sabine Stamm-Dienstleister. Bei kurzfristigem Ausfall kann sie neue Anbieter einsetzen. Für Hochzeiten nutzt sie private Floristik- und Tortenkontakte; bei Firmenveranstaltungen und den beschriebenen Jubiläen ihre reguläre Liste. Die privaten Hochzeitspartner ersetzt sie nicht durch günstigere Fremde. **Belege: M1-F02, F03.**
- **BR2 – Kundenanzahlung:** Nach Auftragsbestätigung werden 30 % Anzahlung berechnet. Ohne Eingang der Anzahlung gibt es keine verbindliche Reservierung von Dienstleistern oder Location. Die Restzahlung ist 14 Tage vor dem Event fällig. Eine ausgestellte Rechnung ist noch kein Zahlungseingang. **Belege: M1-F04, F07.**
- **BR3 – Bestätigungsfrist und Ersatz:** Im Normalfall müssen die Dienstleister spätestens drei Wochen vor dem Event final bestätigt sein. Nach einer kurzfristigen Absage ist spätere Ersatzbeschaffung möglich. Für den beschriebenen Ersatzfall gilt eine schriftliche E-Mail-Zusage als verbindlich; ein Anruf genügt nicht. **Belege: M1-F05, F06.**
- **BR4 – Persönliche Freigabe:** Steigt die Summe der Buchungen, entscheidet Sabine persönlich über die Freigabe. Das gilt auch bei einer preissteigernden Angebotsänderung. Sie delegiert diese Entscheidung nicht. **Belege: M1-F04, F08.**
- **BR5 – Angebotsversion:** Die letzte bestätigte Angebotsversion ist verbindlich; andere Fassungen bleiben Entwürfe. Ein neuerer unbestätigter Entwurf ersetzt die bestätigte Fassung nicht. **Belege: M1-F07, F08.**

## Widersprüche

- **W1 – Harte Frist gegenüber kurzfristigem Ersatz:** „Spätestens drei Wochen“ als harte Bestätigungsgrenze (M1-F05) steht der Technikabsage genau drei Wochen vor dem Stadtfest mit anschließender Ersatzsuche gegenüber (M1-F03). **Auflösung aus M1-F06:** Die Frist gilt im Normalfall, der Ausfall ist eine Ausnahme. Die Anwendung darf eine spätere Ersatzbuchung deshalb nicht blockieren.
- **W2 – Feste Budgetfreigabe gegenüber Änderungen ohne feste Regel:** Sabine genehmigt Budgetanpassungen persönlich (M1-F04), behandelt Angebotsänderungen aber zunächst von Fall zu Fall ohne klare Versionsregel (M1-F07). **Auflösung aus M1-F08:** Kostenfreigabe und Angebotsstand sind getrennte Vorgänge. Sabine gibt Mehrkosten frei; maßgeblich ist die letzte bestätigte Angebotsversion. Ein Entwurf ersetzt sie nicht.

## Prioritäten

**Vorläufiger Modellierungsvorschlag:** Eine ausdrückliche V1/V2-Rangfolge hat Sabine in Meeting 1 noch nicht festgelegt.

1. Kunden, Events und Dienstleisterbuchungen mit Preis, Gewerk, Status und Frist verbinden (**V1-Vorschlag**; M1-F01, F02, F06, F10).
2. Gesamtbudget und Gewerkeplanung den Buchungskosten gegenüberstellen (**V1-Vorschlag**; M1-F04).
3. Aufgaben mit Fälligkeit, Zuständigkeit und Bearbeitungsstand je Event führen (**V1-Vorschlag**; M1-F12).
4. Vollständige Angebots-, Vertrags-, Rechnungs- und Zahlungsverwaltung ausarbeiten (**später/V2 vorgeschlagen**, noch nicht mit Sabine vereinbart; M1-F07–F09, F12).
5. Termine, Locationänderungen, Anbieterübersicht über mehrere Events und differenzierte Ansprechpartner einplanen (**Reihenfolge offen**; M1-F05, F10, F11).

**Annahmen für die Darstellung:** Buchungsstatus zunächst angefragt/bestätigt/abgesagt; Aufgabenstatus offen/in Bearbeitung/blockiert/erledigt. Eine erste Budgetauswertung könnte aktive bestätigte Preise insgesamt und je Gewerk summieren und offene Angebote separat darstellen. Diese Statuskataloge und die genaue Auswahl der Kosten sind noch nicht bestätigt. Die vollständige Angebotsversionierung ist ein bestätigter Bedarf, aber nicht als eigene Entität im begrenzten Kernmodell dargestellt.

## Offene Fragen

- Welche drei Bereiche müssen in V1 zuerst funktionieren; welche können warten?
- Was plant Sabine als Nächstes für Kleist Events? Die Zukunftsfrage wurde in den zwölf Antworten noch nicht gestellt.
- Wann genügen E-Mail-Zusagen und wann sind Unterschriften nötig? Gilt dies für Kunden, Stamm- und neue Dienstleister gleich? M1-F06 beschreibt bislang nur den Ersatzfall ausdrücklich.
- Kann derselbe Anbieter für dasselbe Event mehrere Gewerke übernehmen? Wie sollen getrennte Preise und Zahlungsfristen dann erfasst werden?
- Welche Summe ist die Berechnungsbasis der 30 %? Werden Budget und Preise netto oder brutto geführt? Kundenpreis und internes Kostenbudget sind nicht automatisch identisch.
- Welche Buchungsstatus und Alternativangebote zählen zur Kostensumme? Wie werden Storno, Preisänderungen und Teilzahlungen behandelt? Ein Warnschwellwert ist nicht vereinbart.
- Welche Aufgabenstatus, Erinnerungen und Zugriffsrechte werden benötigt? Zuständigkeit ist noch kein Berechtigungsmodell.
- Wie werden Angebotsbestätigungen, Verträge, Rechnungen und Zahlungen dokumentiert; welche Informationen bleiben in Lexware?
- Wie sollen Terminüberschneidungen, Anbieter-Kapazitäten, wechselnde Ansprechpartner und Locationänderungen abgebildet werden?

## Interviewreferenzen

| Referenz | Thema der Frage und Antwort |
|---|---|
| M1-F01 | Bisheriger Planungsablauf vom Erstgespräch bis zur Eventorganisation |
| M1-F02 | Auswahl, Zusagen und Eventzuordnung von Dienstleistern; private Hochzeitskontakte |
| M1-F03 | Jubiläen, Stammbevorzugung und kurzfristiger Ersatz beim Stadtfest |
| M1-F04 | Budgetplanung, 30 % Anzahlung, Restzahlung und persönliche Freigabe |
| M1-F05 | Termine, Locations und Drei-Wochen-Frist |
| M1-F06 | Klärung der Fristausnahme und schriftliche Ersatz-Zusage |
| M1-F07 | Angebotsinhalte, Bestätigung und nachträgliche Änderungen |
| M1-F08 | Klärung von Kostenfreigabe und verbindlicher Angebotsversion |
| M1-F09 | Rechnungen, Zahlungen und Buchhaltung durch Sabines Mann |
| M1-F10 | Mehrere Events je Kunde/Anbieter und Doppelzusage eines Fotografen |
| M1-F11 | Ansprechpartner, Rollen und Ansprechpartnerwechsel |
| M1-F12 | Bislang übersehene Verträge und Aufgaben je Event |
