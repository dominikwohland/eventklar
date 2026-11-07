# SPEC.md · Kleist Events

HTW Berlin · Smart Applications · Gruppe 3 Eventplanung  
Kundin: Sabine Kleist, Kleist Events

**Quelle:** Meeting 1 mit zwölf und Meeting 2 mit acht Fragen und Antworten. `M1-F01` bis `M1-F12` und `M2-F01` bis `M2-F08` bezeichnen deren Reihenfolge. Stand: 7. November 2026, Fassung nach Meeting 2.

**Ziel:** Kunden, Events, Dienstleisterbuchungen, Budgets und Aufgaben zusammenführen. Sabine soll je Event erkennen, wer welches Gewerk übernimmt, welche Buchung verbindlich ist, welche Kosten entstehen und welche Aufgaben noch offen sind (M1-F01, F06, F12; M2-F07, F08).

## Entitäten

Die fett markierten IDs sind eindeutige Schlüssel. Pro Entität werden drei bis fünf Attribute einschließlich Schlüssel angegeben. Beziehungen stehen im nächsten Abschnitt; technische Fremdschlüssel sind nicht zusätzlich aufgeführt.

| Entität | Attribute |
|---|---|
| Kunde | **kunde_id**, name, email, telefon |
| Event | **event_id**, bezeichnung, eventdatum, gesamtplanbudget, locationangabe |
| Dienstleister | **dienstleister_id**, name, kontaktangabe, erfahrungsstatus (Stamm, Neu), kontaktart (regulär, privat) |
| Gewerk | **gewerk_id**, bezeichnung, beschreibung |
| Buchung | **buchung_id**, vereinbarter_preis, buchungsstatus, zahlungsfrist, verbindlichkeitsgrund |
| Aufgabe | **aufgabe_id**, titel, faelligkeit, bearbeitungsstatus, zustaendigkeit |
| Gewerkbudget | **gewerkbudget_id**, planbetrag, planungsnotiz |
| Reservierungsnachweis | **reservierungsnachweis_id**, kundenunterschrift_am, erforderliche_anzahlung, eingegangene_anzahlung |

**Interviewgrundlage:** Kunde/Event: M1-F01, F10, F11; Dienstleister/Gewerk/Buchung: M1-F02, F03 und M2-F06, F08; Gewerkeplanung: M1-F04; Aufgaben: M1-F12 und M2-F04; Reservierung: M1-F04, F07 und M2-F06.

**Modellierungsannahmen:** Eigene IDs; Gewerkbudget und Reservierungsnachweis als separate Entitäten; Location und Zuständigkeit zunächst als Text. Der Verbindlichkeitsgrund hält fest, ob eine Stamm-Zusage per E-Mail, eine Vertragsunterschrift oder noch kein Nachweis vorliegt. Er bewahrt die Entscheidungsgrundlage auch bei späterer Änderung des Erfahrungsstatus. Vollständige Verträge, Rechnungen, Ansprechpartnerrollen und Vorlagenstrukturen werden im Ausbau ausgearbeitet; das Modell zeigt den V1-Kern.

## Beziehungen

| Nr. | Beziehung | Kardinalität und Bedeutung |
|---|---|---|
| B1 | Kunde — Event | **1 : 0..n**; jedes Event hat genau einen Kunden, ein Kunde kann mehrere Events haben. |
| B2 | Event — Buchung | **1 : 0..n**; jede Buchung gehört genau einem Event. |
| B3 | Dienstleister — Buchung | **1 : 0..n**; jede Buchung betrifft genau einen Dienstleister. Derselbe Anbieter darf im selben Event mehrfach gebucht sein. |
| B4 | Gewerk — Buchung | **1 : 0..n**; jede Buchung betrifft genau ein Gewerk. Mehrere Gewerke werden in getrennten Buchungen erfasst. |
| B5 | Event — Aufgabe | **1 : 0..n**; jede Aufgabe gehört genau einem Event. |
| B6 | Event — Gewerkbudget | **1 : 0..n**; jeder Planansatz gehört genau einem Event. |
| B7 | Gewerk — Gewerkbudget | **1 : 0..n**; jeder Planansatz betrifft genau ein Gewerk. |
| B8 | Event — Reservierungsnachweis | **1 : 0..1**; ein Event hat höchstens einen aktuellen Nachweis, ein Nachweis gehört genau einem Event. |

**n:m:** Event ↔ Dienstleister über Buchung (B2/B3): mehrere Anbieter pro Event, mehrere Events pro Anbieter (M1-F02, F10). Event ↔ Gewerk ist für Planansätze zusätzlich über Gewerkbudget n:m. Diese Erläuterungen werden nicht zusätzlich zu B1–B8 gezählt.

**Mehrere Gewerke:** Thomas kann beim selben Event Catering und Personal übernehmen, mit getrennten Preisen, Zahlungsfristen und späteren Rechnungszuordnungen (M2-F08). Gewählt werden separate Buchungen mit jeweils einem Gewerk. Event und Dienstleister bilden daher keinen eindeutigen Buchungsschlüssel. Auch mehrere Leistungen desselben Gewerks werden nicht durch eine zusätzliche Eindeutigkeitsbedingung ausgeschlossen.

**Weitere Modellierungsannahmen:** Genau ein Auftraggeber pro Event; höchstens ein Planansatz pro Event/Gewerk; ein aktueller Reservierungsnachweis statt vollständiger Zahlungshistorie.

## Business Rules

- **BR1 – Dienstleisterauswahl:** Bei vergleichbarem Preis und vergleichbarer Qualität bevorzugt Sabine Stamm-Dienstleister. Bei kurzfristigem Ausfall kann sie neue Anbieter einsetzen. Für Hochzeiten nutzt sie private Floristik- und Tortenkontakte; bei Firmenveranstaltungen und den beschriebenen Jubiläen ihre reguläre Liste. Die privaten Hochzeitspartner ersetzt sie nicht durch günstigere Fremde. **Belege: M1-F02, F03.**
- **BR2 – Kundenreservierung:** Ohne Kundenunterschrift und eingegangene Anzahlung reserviert Sabine nicht verbindlich. Die Anzahlung beträgt 30 %; die Restzahlung wird 14 Tage vor dem Event fällig. Eine Angebotsannahme oder ausgestellte Anzahlungsrechnung allein genügt nicht. **Belege: M1-F04, F07; Präzisierung zur Unterschrift: M2-F06.**
- **BR3 – Dienstleisterbestätigung:** Im Normalfall müssen Dienstleister spätestens drei Wochen vor dem Event final bestätigt sein. Ein kurzfristiger Ausfall erlaubt spätere Ersatzbeschaffung. Bei Stamm-Dienstleistern genügt eine schriftliche E-Mail-Zusage für Sabines feste Planung; bei neuen Dienstleistern ist die Vertragsunterschrift erforderlich. Eine telefonische Zusage genügt nicht. **Belege: M1-F05, F06; differenzierte Klärung: M2-F06.** Die Ersatzsituation hebt diese spätere Stamm-/Neu-Unterscheidung nicht automatisch auf; eine zusätzliche Ausnahme ist noch zu klären.
- **BR4 – Persönliche Freigabe:** Steigt die Summe der Buchungen, entscheidet Sabine persönlich über die Freigabe. Das gilt auch bei einer preissteigernden Angebotsänderung. Sie delegiert diese Entscheidung nicht. **Belege: M1-F04, F08.**
- **BR5 – Angebotsversion:** Die letzte bestätigte Angebotsversion ist verbindlich; andere Fassungen bleiben Entwürfe. Ein neuerer unbestätigter Entwurf ersetzt die bestätigte Fassung nicht. **Belege: M1-F07, F08.**

**Status und Verbindlichkeit:** Die ausdrücklich genannten Buchungsstatus sind `angefragt`, `zugesagt`, `vertraglich bestätigt` (M2-F06). `Zugesagt` bedeutet bei einem Stamm-Dienstleister mit E-Mail-Nachweis bereits verbindlich, bei einem neuen Anbieter noch nicht. `Abgesagt` wird als Modellierungsannahme ergänzt, um Ausfälle sichtbar zu halten. Ein abgesagter Eintrag zählt nicht als aktive verbindliche Buchung. Der Verbindlichkeitsgrund dokumentiert Sabines betriebliche Entscheidung, keine juristische Bewertung.

## Widersprüche

- **W1 – Harte Frist gegenüber kurzfristigem Ersatz:** „Spätestens drei Wochen“ als harte Bestätigungsgrenze (M1-F05) steht der Technikabsage genau drei Wochen vor dem Stadtfest mit anschließender Ersatzsuche gegenüber (M1-F03). **Auflösung aus M1-F06:** Die Frist gilt im Normalfall, der Ausfall ist eine Ausnahme. Spätere Ersatzbuchungen bleiben erfassbar; die Anwendung darf sie nicht wegen des Stichtags blockieren.
- **W2 – Feste Budgetfreigabe gegenüber Änderungen ohne feste Regel:** Sabine genehmigt Budgetanpassungen persönlich (M1-F04), behandelt Angebotsänderungen aber zunächst von Fall zu Fall ohne klare Versionsregel (M1-F07). **Auflösung aus M1-F08:** Kostenfreigabe und Angebotsstand sind getrennte Vorgänge. Sabine gibt Mehrkosten frei; maßgeblich ist die letzte bestätigte Angebotsversion. Ein Entwurf ersetzt sie nicht.

## Neue Anforderung aus Meeting 2

**NA1 – Wiederverwendbare Eventvorlagen (V2).** Auf die Zukunftsfrage „Was planst du als Nächstes für Kleist Events?“ wünscht Sabine Vorlagen für wiederkehrende Veranstaltungen (M2-F01).

Eine Vorlage liefert Dienstleistervorschläge, Standardaufgaben und wichtige Notizen. Im konkreten Event werden Datum, Location, Gästezahl, Motto und Sonderwünsche angepasst (M2-F02). Beispielhafte Aufgaben sind Location-Begehung vier Monate vorher, Catering-Briefing sechs Wochen vorher und Finalbestätigung drei Wochen vorher. Die erwähnte Erdnussunverträglichkeit ist eine relevante Notiz, keine eigenständige Allergieverwaltung.

Änderungen am konkreten Event wirken nicht automatisch auf die Vorlage zurück; Vorlagenänderungen erfolgen bewusst. Ist ein vorgeschlagener Anbieter nicht verfügbar, wird er im konkreten Event ersetzt (M2-F03). Ein Vorschlag gilt noch nicht als verbindliche Buchung. In M2-F07 ordnet Sabine Vorlagen ausdrücklich V2 zu. Die V2-Vorlagenstruktur wird später ergänzt und ist keine neunte Entität des V1-Modells.

## Prioritäten

Die drei V1-Bereiche und die ausdrücklich zurückgestellten Themen wurden von Sabine in M2-F07 festgelegt.

1. **Eventübersicht und Dienstleisterbuchungen (V1):** Gewerk, Preis, Zahlungsfrist, Status und Verbindlichkeit je Buchung; mehrere getrennte Buchungen desselben Anbieters pro Event (M1-F02, F06; M2-F06–F08).
2. **Budgettracking (V1):** Gesamtplanbudget gegen Buchungssumme und Kostenaufschlüsselung nach Gewerk (M1-F04; M2-F07, F08). Buchungskosten und bereits gezahlte Beträge bleiben unterscheidbar.
3. **Aufgaben pro Event (V1):** Fälligkeit, Zuständigkeit und Bearbeitungsstand; auch Aufgaben ohne Dienstleisterbuchung (M1-F12; M2-F04, F07).
4. **Verträge, Nachträge, Angebotsversionen sowie Rechnungen und Zahlungen (V2):** bestehende Anforderungen aus M1-F07–F09, F12 und M2-F05; vollständige Vertrags-/Rechnungsverwaltung in M2-F07 zurückgestellt. Der minimale Nachweis zur V1-Verbindlichkeit bleibt erforderlich. Eine Lexware-Schnittstelle ist nicht vereinbart.
5. **Eventvorlagen und differenzierte Zugriffsrechte (V2):** M2-F01–F04, F07. Sabine benötigt die Gesamtsicht; ihr Mann Rechnungen/Buchungszahlen, ihre Tochter Ablauf und Dienstleisterkontakte ohne Budgetzahlen. V1 ist als Arbeitsmittel für Sabine abgegrenzt; dieser konkrete Betriebsumfang ist eine Modellierungsannahme.
6. **Noch zu terminieren:** eventübergreifende Anbieter-/Verfügbarkeitsübersicht, Termine/Locationänderungen und mehrere Ansprechpartner mit Rollen (M1-F05, F10, F11; M2-F03). Keine ausdrückliche Versionszusage ableiten.

**Konkretisierung der V1-Auswertung:** Preise werden je Buchung gespeichert und nach Gewerk sowie insgesamt summiert. Bei Catering zu 8.000 EUR und Personal zu 2.000 EUR desselben Anbieters bleiben beide Positionen sichtbar; zusammen ergeben sie 10.000 EUR. Bei 20.000 EUR Planbudget wären das 50 % und 10.000 EUR verbleibend. Die Werte sind ein Rechenbeispiel, keine Interviewbeträge.

**Modellierungsannahmen:** Als führende Kostensumme zunächst aktive verbindliche Buchungen verwenden, unverbindliche Anfragen/Zusagen gesondert darstellen; die genaue Kostenauswahl ist noch abzustimmen. Bei einem Budget von null wird kein Prozentwert berechnet. Aufgabenstatus zunächst offen/in Bearbeitung/blockiert/erledigt; Überfälligkeit aus Fälligkeit und Bearbeitungsstand ableiten. Die im Interview genannten 85 % sind kein vereinbarter Warnschwellwert; auch 130 % begründen keine automatische Buchungssperre.

## Offene Fragen

- Auf welchen Kundenbetrag beziehen sich die 30 %? Wie werden Preisänderungen und Teilzahlungen berücksichtigt? Internes Kostenbudget und Kundenpreis sind nicht automatisch identisch.
- Werden Budget und Preise netto oder brutto geführt und in welcher Währung?
- Welche Buchungsstatus und Alternativangebote zählen zur führenden Kostensumme? Wie werden Stornokosten behandelt? Rechnungen dürfen später nicht zusätzlich zum zugehörigen Buchungspreis als zweite Leistung gezählt werden.
- Gilt bei einem neuen Ersatzanbieter in einer Krise weiterhin zwingend die Unterschrift? M1-F06 nennt E-Mail beim Ersatz, M2-F06 unterscheidet später ausdrücklich Stamm/Neu. Bis zur Klärung gilt die spezifischere Erklärung aus M2-F06; eine weitere Ausnahme wird nicht erfunden.
- Wer und nach welchen Kriterien stuft einen neuen Anbieter als Stamm ein?
- Welche Aufgabenstatus, Pflichtangaben und Erinnerungen werden benötigt?
- Wann werden Verfügbarkeitsübersicht, Termine/Locations und mehrere Ansprechpartner umgesetzt? Welche Zeiträume und Kapazitäten müssen berücksichtigt werden? Gleiches Datum bedeutet nicht automatisch vollständige Nichtverfügbarkeit.
- Welche Angebots-, Vertrags-, Rechnungs- und Zahlungsinformationen speichert die App selbst; welche bleiben in Lexware oder anderen Ablagen?
- Wie werden Vorlagen gepflegt, relative Monatsfristen berechnet und sensible Notizen über die späteren Zugriffsrechte geschützt?

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
| M2-F01 | Zukunftsfrage und erstmals Eventvorlagen |
| M2-F02 | Inhalte einer Vorlage am Beispiel TechCorp-Weihnachtsfeier |
| M2-F03 | Unabhängige Eventänderungen, bewusste Vorlagenpflege, Verfügbarkeit |
| M2-F04 | Aufgabenverteilung und unterschiedliche Zugriffsbedarfe |
| M2-F05 | Vertragsentstehung, Unterschriften und Nachträge |
| M2-F06 | Präzisierung der Verbindlichkeit bei Stamm-/neuen Dienstleistern und Kunden |
| M2-F07 | Verbindliche V1-Prioritäten und zurückgestellte V2-Themen |
| M2-F08 | Mehrere Gewerke je Anbieter/Event, getrennte Buchungen und Budget nach Gewerk |
