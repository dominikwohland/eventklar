# SPEC.md – Kleist Events

**HTW Berlin · Smart Applications · Gruppe 3: Eventplanung**  
**Kundin:** Sabine Kleist, Kleist Events  
**Fassung:** v1 nach Meeting 1  
**Erstellt:** 7. November 2026 als nachträgliche Rekonstruktion des fachlichen Stands nach Meeting 1

## 1. Grundlage und Ziel

Diese Fassung verwendet ausschließlich die zwölf Antworten aus Meeting 1. Sie rekonstruiert deren damaligen Informationsstand und behauptet keine frühere Erstellung oder Abgabe. Die Referenzen M1-F01 bis M1-F12 entsprechen der Reihenfolge der Fragen und Antworten. Erkenntnisse aus dem zweiten Gespräch werden nicht vorweggenommen.

Kleist Events organisiert unter anderem Firmenveranstaltungen, Hochzeiten und Stadtfeste. Sabine verwaltet Veranstaltungen in einzelnen Excel-Dateien, Zusagen und Vertragsunterlagen in E-Mails, Kontakte und Termine in Outlook sowie Aufgaben im Notizbuch. Informationen sind dadurch verteilt und teilweise veraltet. Die Anwendung soll Kunden, Events, Dienstleisterbuchungen, Kosten und Aufgaben nachvollziehbar verbinden. Sabines Mann übernimmt die Buchhaltung, derzeit mit Lexware (M1-F01, M1-F06, M1-F09, M1-F12).

**Bestätigte Fakten** sind mit Interviewreferenzen belegt. **Modellierungsannahmen** sind fachliche Entwurfsentscheidungen, die noch bestätigt werden müssen. **Offene Fragen** bleiben ausdrücklich offen. Die ausgewählten Attribute bilden keinen vollständigen Datensatz ab.

Die Struktur folgt den bereits ausgewerteten Kursunterlagen: Modul 3, S. 17–20, und Anforderungsmodell, S. 3–11 sowie 13–14. Gefordert ist ein fachliches ER-Modell mit Identitäten und Kardinalitäten, noch kein technisches Datenbankschema. Die Kursunterlagen ersetzen keine Kundinnenaussagen.

## 2. Fachliches Modell

### 2.1 Entitäten: acht

Die Attribute schließen den eindeutigen Schlüssel ein. Beziehungen werden separat beschrieben; Fremdschlüssel werden hier nicht zusätzlich als Attribute aufgeführt. Die Vergabe stabiler IDs ist eine Modellierungsannahme.

| Nr. | Entität | Attribute einschließlich Schlüssel | Anzahl | Beleg und Einordnung |
|---|---|---|---:|---|
| E1 | Kunde | **KundeID**, Name, E-Mail, Telefon | 4 | Wiederkehrende Kunden und Kontaktdaten: M1-F10, M1-F11. Mehrere Ansprechpartner sind als weiterer Bedarf dokumentiert. |
| E2 | Event | **EventID**, Bezeichnung, Eventdatum, Gesamtplanbudget, Locationangabe | 5 | M1-F01, M1-F04, M1-F05, M1-F07. Location zunächst als Text; keine eigene Locationverwaltung vorausgesetzt. |
| E3 | Dienstleister | **DienstleisterID**, Name, Kontaktangabe, Erfahrungsstatus, Kontaktart | 5 | Stamm-/neue Anbieter und private/reguläre Kontakte: M1-F02, M1-F03. Die beiden Merkmale sind unabhängig. Eine besondere Vertragslogik je Erfahrungsstatus ist noch nicht bestätigt. |
| E4 | Gewerk | **GewerkID**, Bezeichnung, Beschreibung | 3 | Catering, Technik, Foto, Personal und weitere Leistungen: M1-F02, M1-F04. Eigener Katalog ist eine Modellierungsannahme. |
| E5 | Buchung | **BuchungID**, VereinbarterPreis, Buchungsstatus, Zahlungsfrist, SchriftlicheZusageAm | 5 | Preis, Frist und Gewerkzuordnung: M1-F02; Status und schriftliche Bestätigung: M1-F06. Eigene Buchungsidentität ermöglicht die Trennung von Stammdaten und Eventzuordnung. |
| E6 | Aufgabe | **AufgabeID**, Titel, Fälligkeit, Bearbeitungsstatus, Zuständigkeit | 5 | Aufgaben, Delegation, offene und blockierte Arbeit: M1-F12. Zuständigkeit zunächst als Text, keine Benutzerverwaltung unterstellt. |
| E7 | Gewerkbudget | **GewerkbudgetID**, Planbetrag, Planungsnotiz | 3 | Geplante Aufteilung auf Gewerke aus M1-F04. Eigene Zuordnungsentität ist eine Modellierungsannahme. |
| E8 | Reservierungsnachweis | **ReservierungsnachweisID**, AuftragsbestätigungAm, ErforderlicheAnzahlung, EingegangeneAnzahlung | 4 | Auftragsbestätigung und 30 % Anzahlung: M1-F04, M1-F07. Minimaler aktueller Nachweis; keine vollständige Rechnung oder Zahlungshistorie. |

### 2.2 Beziehungen: acht mit Mindest- und Höchstkardinalität

| Nr. | Beziehung | Bedeutung |
|---|---|---|
| B1 | **Kunde 1 — 0..n Event** | Ein Kunde kann mehrere Events beauftragen (M1-F10). Jedes Event hat im Modell genau einen Auftraggeber; letzteres ist eine Annahme. |
| B2 | **Event 1 — 0..n Buchung** | Jede Buchung gehört zu genau einem Event; ein Event kann zunächst ohne Buchungen bestehen (M1-F01, M1-F02). |
| B3 | **Dienstleister 1 — 0..n Buchung** | Jede Buchung betrifft einen Dienstleister. Derselbe Anbieter kann an mehreren Events mitwirken (M1-F10). |
| B4 | **Gewerk 1 — 0..n Buchung** | Jede Buchung wird im Modell einem Gewerk zugeordnet (M1-F02). Ob ein Anbieter mehrere Gewerke innerhalb desselben Events übernimmt und wie das zu erfassen ist, bleibt eine offene Frage. |
| B5 | **Event 1 — 0..n Aufgabe** | Jede konkrete Aufgabe gehört genau zu einem Event (M1-F12). |
| B6 | **Event 1 — 0..n Gewerkbudget** | Ein Event besitzt mehrere gewerkbezogene Planansätze; jeder Ansatz gehört genau einem Event (M1-F04). |
| B7 | **Gewerk 1 — 0..n Gewerkbudget** | Ein Ansatz betrifft genau ein Gewerk. Höchstens ein Ansatz pro Event/Gewerk ist eine Modellierungsannahme (M1-F04). |
| B8 | **Event 1 — 0..1 Reservierungsnachweis** | Ein Event kann vor der Bestätigung ohne Nachweis bestehen; jeder Nachweis gehört einem Event. Die Beschränkung auf einen aktuellen Nachweis ist eine Annahme (M1-F04, M1-F07). |

**n:m:** Event und Dienstleister stehen fachlich in einer n:m-Beziehung, aufgelöst durch Buchung über B2/B3: mehrere Anbieter pro Veranstaltung und mehrere Veranstaltungen pro Anbieter (M1-F02, M1-F10). Event und Gewerk sind für die Planansätze zusätzlich über Gewerkbudget verbunden. Diese Zusammenhänge werden nicht als weitere Beziehungen neben B1–B8 doppelt gezählt.

Die BuchungID ist unabhängig von Event und Anbieter. Das Modell führt keine Eindeutigkeit allein auf diesem Paar ein. Dies ist eine vorsichtige Modellierungsentscheidung, noch keine bestätigte Anforderung nach mehreren Gewerken je Anbieter und Event.

### 2.3 Status und Kosten

**Bestätigt:** Sabine muss angefragte, zugesagte und ausgefallene Dienstleister auseinanderhalten können. Insbesondere bei Ersatzbeschaffung drohen parallele Anfragen und eine unbeabsichtigte Doppelbuchung (M1-F03, M1-F06). Für den dort beschriebenen Ersatzfall genügt ihr eine schriftliche E-Mail-Zusage; ein Telefonat reicht nicht (M1-F06). Die Übertragung dieser Aussage auf alle Vertragsfälle ist noch zu klären.

**Modellierungsannahme:** Buchungsstatus zunächst `angefragt`, `bestätigt`, `abgesagt`. Aufgabenstatus zunächst `offen`, `in Bearbeitung`, `blockiert`, `erledigt`. Das Interview legt keine vollständigen Statuskataloge fest. Abgesagte Buchungen bleiben für die Nachvollziehbarkeit erhalten; Ersatz wird separat erfasst. Zuständigkeit beschreibt Arbeitszuordnung und verleiht noch keine Zugriffsrechte.

**Bestätigt:** Sabine plant ein Gesamtbudget und teilt es auf Gewerke auf. Tatsächliche Angebotspreise stellt sie daneben; Änderungen und Zusatzleistungen beeinflussen die Kosten (M1-F04). Die Anwendung soll diese Zuordnung erhalten.

**Modellierungsannahme:** Eine erste Budgetauswertung summiert aktive bestätigte Buchungspreise insgesamt und je Gewerk. Offene Anfragen werden getrennt als mögliche Kosten dargestellt. Die Differenz zum Gesamtplanbudget und bei einem Budget größer null die prozentuale Auslastung werden abgeleitet. Ob Sabine genau diese Kostenauswahl will, bleibt offen. Stornogebühren und Netto-/Bruttobasis sind gesondert zu klären. Aus der berichteten Überschreitung auf 130 % wird weder eine bestimmte Warnschwelle noch eine automatische Sperre erfunden.

## 3. Geschäftsregeln: genau fünf

### GR-01 – Dienstleisterauswahl berücksichtigt bewährte und private Kontakte

Bei vergleichbarem Preis und vergleichbarer Qualität bevorzugt Sabine Stamm-Dienstleister. Nach kurzfristiger Absage ist ein neuer Ersatzanbieter möglich. Bei Hochzeiten verwendet sie ihre privaten Kontakte für Floristik und Torte; bei Firmenveranstaltungen und den beschriebenen Jubiläen arbeitet sie über ihre reguläre Liste. Ihre privaten Hochzeitskontakte ersetzt sie nach eigener Aussage nicht durch günstigere Fremde.

**Belege:** M1-F02 beschreibt die privaten Hochzeitskontakte; M1-F03 nennt Stammbevorzugung, reguläre Auswahl bei Jubiläen, kurzfristigen neuen Ersatz und die Sonderstellung der privaten Hochzeitskontakte.

**Konsequenz:** Erfahrungsstatus und Kontaktart getrennt führen. Daraus folgt kein automatischer Auswahlalgorithmus und keine allgemeine Verpflichtung, stets den billigsten Anbieter zu nehmen.

### GR-02 – Ohne 30 % Anzahlung keine verbindliche Reservierung

Nach Kundenbestätigung stellt Sabine 30 % Anzahlung in Rechnung. Ohne deren Eingang reserviert sie nicht verbindlich und blockiert weder Dienstleister noch Location. Die Restzahlung wird 14 Tage vor dem Event fällig.

**Belege:** M1-F04: „30% Anzahlung“, ohne diese „keine verbindliche Reservierung“, Restzahlung „14 Tage vor dem Eventdatum“; M1-F07: „Ohne Anzahlung blocke ich keinen Dienstleister, keine Location“.

**Konsequenz:** Rechnungserstellung und Zahlungseingang unterscheiden. Eine zusätzliche zwingende Kundenunterschrift lässt sich aus Meeting 1 nicht ableiten: M1-F07 nennt Bestätigung per E-Mail oder Vertragsunterschrift. Die Berechnungsbasis der Anzahlung bleibt offen; das interne Kostenbudget wird nicht automatisch zum Kundenpreis erklärt.

### GR-03 – Finalbestätigung drei Wochen vorher, Ersatzfall als Ausnahme

Im Normalfall sollen alle Dienstleister spätestens drei Wochen vor dem Event final bestätigt sein. Nach einem kurzfristigen Ausfall kann danach noch Ersatz organisiert werden. Für den in M1-F06 beschriebenen Ersatz gilt die schriftliche E-Mail-Zusage als verbindliche Bestätigung; ein Anruf genügt nicht.

**Belege:** M1-F05 nennt die Drei-Wochen-Grenze; M1-F06 grenzt Normalfall und Ausnahmesituation ab und erläutert die schriftliche Zusage.

**Konsequenz:** Eine Fristüberschreitung darf die Erfassung eines Ersatzes nicht verhindern. Darstellung als Fristproblem und Erhalt des abgesagten Eintrags sind Modellierungsannahmen. Eine generelle Vertragsausnahme für jeden neuen Anbieter ist noch nicht geklärt.

### GR-04 – Kostensteigerungen gibt Sabine persönlich frei

Steigt die Summe der Buchungen, entscheidet Sabine selbst über die Freigabe. Auch eine preissteigernde Angebotsänderung muss sie freigeben. Diese Entscheidung delegiert sie nicht. Kundenwünsche nach Zusatzleistungen werden besprochen und können zu einer Budgetanpassung führen.

**Belege:** M1-F04: Budgetanpassung „persönlich absegnen“, „delegiere ich nicht“; M1-F08: „Wenn die Summe meiner Buchungen nach oben geht, entscheide das ich“ und Anwendung der Budgetregel bei Preissteigerung durch Änderung.

**Konsequenz:** Kostenpflege und spätere Freigabefunktionen müssen Sabines Entscheidung berücksichtigen. Daraus folgt noch kein technisch vereinbarter Mehrpersonenprozess.

### GR-05 – Die letzte bestätigte Angebotsversion ist maßgeblich

Für den Kundenauftrag gilt die letzte bestätigte Angebotsversion. Andere Fassungen bleiben Entwürfe; eine spätere Erstellung allein macht eine Fassung nicht verbindlich. Bei preissteigernden Änderungen kommt zusätzlich GR-04 zur Anwendung.

**Belege:** M1-F07 schildert die Unklarheit mehrerer Fassungen; M1-F08 formuliert den gewünschten eindeutigen Stand: „Die letzte bestätigte Version ist verbindlich, alles andere ist Entwurf“.

**Konsequenz:** Die spätere Angebotsverwaltung benötigt unterscheidbare Versionen und Bestätigungsstände. Das begrenzte Kernmodell oben enthält noch keine vollständige Angebotsentität; der Bedarf bleibt im Ausbauumfang dokumentiert.

## 4. Aufgelöste Widersprüche: genau zwei

### W1 – Harte Bestätigungsfrist gegenüber kurzfristiger Ersatzsuche

**Ausgangsaussagen:** M1-F05 nennt drei Wochen vor dem Event eine harte Grenze. M1-F03 berichtet von einer Technikabsage genau zu diesem Zeitpunkt und anschließender Ersatzsuche.

**Direkte Klärung:** Auf die Rückfrage in M1-F06 erklärt Sabine, dass die Drei-Wochen-Grenze für den Normalfall gilt; eine Absage erzeugt eine Ausnahmesituation.

**Gültige Auslegung:** Frist im Normalbetrieb, erlaubte Ersatzbeschaffung im Ausfallfall. Keine absolute Buchungssperre nach dem Stichtag. Die schriftliche Zusage im beschriebenen Ersatzfall liefert den Bestätigungsnachweis (GR-03).

### W2 – Persönliche Budgetfreigabe gegenüber Änderungen „ohne feste Regel“

**Ausgangsaussagen:** M1-F04 verlangt Sabines persönliche Freigabe bei Budgetanpassungen. M1-F07 schildert Angebotsänderungen dagegen als Fall-zu-Fall-Entscheidung ohne feste Regel und mit unklarem Versionsstand.

**Direkte Klärung:** M1-F08 unterscheidet ihre interne Kostenentscheidung von der bisher ungeordneten Angebotsbearbeitung. Sabine legt ausdrücklich fest, dass die letzte bestätigte Angebotsversion verbindlich sein soll und Preissteigerungen ihre Freigabe benötigen.

**Gültige Auslegung:** Eine redaktionelle Änderung und die Entscheidung über Mehrkosten sind unterschiedliche Vorgänge. GR-04 regelt die Freigabe, GR-05 den maßgeblichen Angebotsstand. Eine neue Entwurfsfassung setzt die bestätigte Fassung nicht außer Kraft.

## 5. Vorläufige Prioritäten

Meeting 1 enthält noch keine ausdrückliche Rangfolge von drei V1-Bereichen. Die folgende Staffelung ist daher ein **Vorschlag zur Abstimmung**, keine bestätigte Versionszusage der Kundin.

| Stufe | Vorschlag | Grundlage |
|---|---|---|
| V1-Vorschlag: zuerst | Kunden und Events verbinden; Dienstleisterbuchungen mit Gewerk, Preis, Frist und Bestätigungsstand sichtbar machen | M1-F01, M1-F02, M1-F06, M1-F10 |
| V1-Vorschlag: danach | Gesamtbudget und gewerkbezogene Planansätze den Buchungskosten gegenüberstellen | M1-F04 |
| V1-Vorschlag: ergänzend | Aufgaben pro Event mit Fälligkeit, Zuständigkeit und Bearbeitungsstand führen | M1-F12 |
| Ausbau / V2-Vorschlag | Vollständige Angebote und Versionen, Verträge und Unterschriftenstände, Rechnungs-/Zahlungsübersicht | M1-F07–F09, M1-F12. Diese Bedürfnisse sind bestätigt; ihre spätere Umsetzung ist lediglich vorgeschlagen. |
| Noch einzuplanen | Termine, Locationänderungen, eventübergreifende Anbieterübersicht und mehrere Ansprechpartner mit Rollen | M1-F05, M1-F10, M1-F11 |

Die im Modell nicht eigenständig dargestellten Geschäftsthemen sind damit nicht verworfen. Es wurde weder eine Lexware-Schnittstelle noch ein vollständiger Ersatz der Buchhaltung zugesagt.

## 6. Beispiele zur fachlichen Prüfung

Die Beträge und Termine sind illustrative Testdaten, keine zusätzlichen Interviewfakten.

| Fall | Erwartung | Beleg / Annahme |
|---|---|---|
| Ein Firmenkunde beauftragt Sommerfest und Weihnachtsfeier | Ein Kundenstammsatz, zwei getrennte Events mit eigenen Buchungen und Aufgaben | M1-F10 |
| Ein Caterer arbeitet bei vier Events mit | Gemeinsame Anbieterdaten, getrennte Eventbuchungen und Preise | M1-F02, M1-F10 |
| Technik fällt zehn Tage vor dem Event aus | Ersatz bleibt erfassbar; schriftliche Zusage wird festgehalten | M1-F06; GR-03 |
| Erforderliche Anzahlung 3.000 EUR, Eingang 0 EUR | Keine verbindliche Reservierung allein wegen der ausgestellten Rechnung | M1-F04, M1-F07; GR-02 |
| Bestätigte Fassung A, späterer unbestätigter Entwurf B | A bleibt maßgeblich; eine Mehrkostenfreigabe wird nicht aus dem bloßen Entwurf abgeleitet | M1-F08; GR-04, GR-05 |
| Aufgabe mit abgelaufener Frist und Status offen | Im zugehörigen Event als noch zu erledigen erkennbar; Delegation bleibt sichtbar | M1-F12; konkrete Statusdarstellung ist eine Annahme |

## 7. Offene Fragen für das Folgegespräch

| ID | Frage | Anlass |
|---|---|---|
| O1 | Welche Bereiche müssen zuerst funktionieren und welche dürfen warten? | Noch keine ausdrückliche V1/V2-Priorisierung in Meeting 1. |
| O2 | Was plant Sabine als Nächstes für Kleist Events; ergeben sich daraus weitere Anforderungen? | Zukunftsfrage ist in den zwölf vorliegenden Antworten noch nicht gestellt. Eine Antwort wird hier nicht erfunden. |
| O3 | Wann genügen E-Mail-Zusagen, wann ist eine Unterschrift nötig? Gilt das bei Kunden, Stamm- und neuen Dienstleistern gleich? | M1-F06 nennt E-Mail für Ersatz, M1-F07 E-Mail oder Unterschrift beim Kunden, M1-F12 schriftliche Verträge mit allen Beteiligten. Die genaue Verbindlichkeitslogik ist noch offen. |
| O4 | Kann ein Anbieter mehrere Gewerke desselben Events übernehmen; braucht es dafür getrennte Buchungen, Preise und Zahlungsfristen? | M1-F02 nennt Gewerk/Preis/Frist je Zuordnung, beschreibt diesen Mehrfachfall aber nicht ausdrücklich. |
| O5 | Welche Betragsbasis gilt: netto oder brutto, welche Währung? Welche Summe bestimmt die 30 %? | M1-F04, M1-F09. Kundenpreis und internes Kostenbudget müssen auseinandergehalten werden. |
| O6 | Welche Angebote und Buchungsstatus gehören in die Kostensumme? Wie werden Storno, Teilzahlungen und Preisänderungen behandelt? | M1-F04–F07. Ein bestimmter Warnschwellwert wurde nicht festgelegt. |
| O7 | Welche Aufgabenstatus sind nötig; wer darf Informationen sehen, ändern oder freigeben? | M1-F09, M1-F11, M1-F12. Tätigkeiten und Kundenrollen sind beschrieben, App-Berechtigungen noch nicht. |
| O8 | Welche Vertrags- und Angebotsinformationen werden gespeichert und wie wird die Bestätigung einer Version nachgewiesen? | M1-F07, M1-F08, M1-F12. |
| O9 | Welche Termin-/Kapazitätsinformationen verhindern Doppelzusagen? Wie werden Locationänderungen und wechselnde Ansprechpartner erhalten? | M1-F05, M1-F10, M1-F11. Ein Datum allein beweist keine Nichtverfügbarkeit. |
| O10 | Wie weit soll die Anwendung Rechnungen und Zahlungen selbst verwalten; bleibt Lexware das führende System? | M1-F09. Ein technischer Datenaustausch ist nicht vereinbart. |

## 8. Interviewindex Meeting 1

| Referenz | Frage / Suchanker | Wesentliche Erkenntnis |
|---|---|---|
| M1-F01 | „Wie lief bei dir bisher die Planung …?“ | Erstgespräch, Angebot, Excel pro Event, Anbieteranfragen, verteilte Termine. |
| M1-F02 | „… Dienstleister ausgewählt, ihre Zusagen festgehalten …?“ | Stammanbieter, Gewerk, Preis, Zahlungsfrist, private Hochzeitskontakte. |
| M1-F03 | „… bei anderen Veranstaltungen wie Jubiläen …?“ | Stammbevorzugung, reguläre Liste, neuer Ersatz bei Ausfall, private Hochzeitspartner. |
| M1-F04 | „… Budget geplant … Zahlungen organisiert …?“ | Gesamt-/Gewerkeplanung, 30 % Anzahlung, Restzahlung 14 Tage vorher, Sabines Freigabe. |
| M1-F05 | „… Termine und Locations … koordiniert …?“ | Terminkollisionen, falsche Locationadresse, Drei-Wochen-Grenze. |
| M1-F06 | „… feste Frist … Ersatzdienstleister … verbindlich …?“ | Ausfallausnahme, schriftliche Ersatz-Zusage, fehlende Statusübersicht. |
| M1-F07 | „… Informationen in diesem Angebot … Änderungen …?“ | Angebotsinhalte, Bestätigung, Anzahlung, ungeordnete Änderungsfassungen. |
| M1-F08 | „… Budgetänderungen … keine feste Regel …?“ | Persönliche Mehrkostenfreigabe, letzte bestätigte Version gilt. |
| M1-F09 | „… Rechnungen und Zahlungen … verwaltet …?“ | Buchhaltung durch den Mann, Lexware, Rechnungsdaten, fehlende Zahlungsübersicht. |
| M1-F10 | „… Kunde mehrere Events … derselbe Dienstleister …?“ | Mehrfachbeauftragungen und übersehene Doppelzusage eines Fotografen. |
| M1-F11 | „… Ansprechpartner … mehrere Personen …?“ | Kontaktdaten, unterschiedliche Kundenrollen, Ansprechpartnerwechsel. |
| M1-F12 | „… noch etwas Wichtiges … übersehen …?“ | Verträge und Unterschriftenstände, eventbezogene Aufgaben mit Delegation und Fristen. |

## 9. Umfangsprüfung

- Acht Entitäten, jeweils drei bis fünf Attribute einschließlich eindeutigem Schlüssel.
- Acht Beziehungen mit Kardinalitäten; n:m über Buchung sowie über Gewerkbudget erläutert.
- Genau fünf Geschäftsregeln, alle ausschließlich mit Antworten aus Meeting 1 belegt.
- Genau zwei explizit aufgelöste Widersprüche aus Meeting 1.
- Priorisierung als noch nicht bestätigter Vorschlag gekennzeichnet; offene Fragen für das Folgegespräch dokumentiert.
- Keine neue Anforderung aus dem Folgegespräch vorweggenommen und keine nachträgliche Erkenntnis als damalige Aussage dargestellt.
