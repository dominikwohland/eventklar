# SPEC.md – Kleist Events

**HTW Berlin · Smart Applications · Gruppe 3: Eventplanung**  
**Kundin:** Sabine Kleist, Kleist Events  
**Stand:** 6. November 2026 · Anforderungsspezifikation, Version 1.0

## 1. Auftrag und Quellenlage

Kleist Events plant unter anderem Firmenevents, Hochzeiten und Stadtfeste. Sabine führt Eventinformationen heute verteilt in Excel, Outlook, E-Mails und einem Notizbuch. Dadurch sind Zusagen, Zahlungsfristen, Kosten und Aufgaben nicht zuverlässig gemeinsam sichtbar. Die Anwendung soll für jedes Event einen zusammenhängenden Arbeitsstand liefern und die parallele Planung mehrerer Veranstaltungen erleichtern.

Diese Spezifikation wertet die **20 im Chat vorliegenden Interviewantworten** aus: zwölf aus Meeting 1 und acht aus Meeting 2. Die nachgereichte vollständige Transkription ist die maßgebliche Primärquelle; frühere Zusammenfassungen des Assistenten sind keine Interviewbelege. Die Referenzen `M1-F01` bis `M1-F12` und `M2-F01` bis `M2-F08` bezeichnen jeweils Frage und zugehörige Antwort in ihrer Gesprächsreihenfolge. Der Quellenindex in Abschnitt 10 macht diese Zuordnung nachvollziehbar.

Im lokalen Projektordner sind keine Kursunterlagen oder Dateien zum Anforderungsmodell zugänglich; `sources/` ist leer. Eine Übereinstimmung mit nicht vorliegenden Kursunterlagen wird daher nicht behauptet. Die ausdrücklich vorgegebenen Strukturgrenzen werden erfüllt: **acht Entitäten, jeweils drei bis fünf Attribute einschließlich Primärschlüssel, acht Beziehungen, n:m-Zusammenhänge, fünf Geschäftsregeln, zwei aufgelöste Widersprüche und eine als neu ausgewiesene Anforderung aus Meeting 2.**

**Kennzeichnung:** „Bestätigt“ bezeichnet Aussagen aus dem Interview. „Modellierungsannahme“ bezeichnet eine hier gewählte Umsetzung, die keine zusätzliche Kundinnenforderung darstellt. „Offen“ bezeichnet eine noch zu treffende Entscheidung. Akzeptanzbeispiele verwenden illustrative Testwerte.

## 2. Zielbild und Umfang

Sabine soll ein Event öffnen und unmittelbar erkennen können, welche Dienstleister für welche Gewerke angefragt oder gebucht sind, welche Zusagen verbindlich sind, welche Kosten entstehen und welche Aufgaben noch offenstehen. Eine Dienstleisterbuchung darf nicht auf eine einzige Verbindung zwischen Event und Dienstleister reduziert werden: Thomas kann für dasselbe Event Catering und Personal übernehmen, mit getrennten Preisen und Zahlungsfristen. Die Budgetansicht zeigt deshalb sowohl die Gesamtsumme als auch die Verteilung nach Gewerk. **Bestätigt:** M1-F01, M1-F02, M1-F06, M1-F12, M2-F07, M2-F08.

Sabine ist die primäre Nutzerin der ersten Version. Ihr Mann übernimmt die Buchhaltung; ihre Tochter unterstützt bei größeren Events. Die später benötigten Zugriffsrechte unterscheiden sich: Sabine sieht alles, ihr Mann Rechnungen und Buchungszahlen, ihre Tochter Ablauf und Dienstleisterkontakte ohne Budgetzahlen. **Bestätigt:** M1-F09, M2-F04. Daraus folgt noch kein freigegebenes technisches Rollenmodell.

### 2.1 Prioritäten

| Version | Umfang | Interviewbeleg und Abgrenzung |
|---|---|---|
| **V1 – Priorität 1** | Eventübersicht mit Dienstleisterbuchungen, Gewerk, Preis, Zahlungsfrist und Status; mehrere Buchungen desselben Dienstleisters je Event | M1-F02, M1-F06, M2-F06–F08. Minimaler Nachweis der Verbindlichkeit gehört zur Buchung; vollständige Vertragsverwaltung folgt später. |
| **V1 – Priorität 2** | Planbudget gegen Buchungskosten, Gesamtauslastung und Aufschlüsselung nach Gewerk | M1-F04, M2-F07, M2-F08. Buchungskosten sind keine bereits geleisteten Zahlungen. |
| **V1 – Priorität 3** | Aufgaben je Event mit Zuständigkeit, Fälligkeit und Bearbeitungsstand | M1-F12, M2-F04, M2-F07. Aufgaben müssen auch ohne Dienstleisterbuchung existieren können. |
| **V1 – notwendige Grundlage** | Gemeinsame Kunden- und Dienstleisterdaten; Stamm-/Neu-Unterscheidung; manueller Reservierungsnachweis | M1-F02, M1-F04, M1-F10, M2-F06. Keine eigene Buchhaltungssoftware. |
| **V2** | Verträge und Nachträge, Angebotsversionen, Rechnungs- und Zahlungsübersicht | M1-F07–F09, M1-F12, M2-F05, M2-F07. Lexware bleibt bis dahin der bestehende Buchhaltungsablauf; eine Schnittstelle wurde nicht zugesagt. |
| **V2** | Eventvorlagen gemäß Abschnitt 7 | M2-F01–F03, ausdrücklich zurückgestellt in M2-F07. |
| **V2** | Differenzierte Zugriffsrechte | M2-F04, M2-F07. V1 ist als Arbeitsmittel für Sabine abgegrenzt; der Mehrpersonenbetrieb benötigt eine gesonderte Ausarbeitung. |
| **Noch einzuplanen** | Eventübergreifende Verfügbarkeitsübersicht, Termine/Locations und differenzierte Ansprechpartner | M1-F05, M1-F10, M1-F11, M2-F03. Keine eindeutige Versionszusage im Interview; nicht stillschweigend zum V1-Pflichtumfang erklärt. |

## 3. Fachliches Datenmodell

Das Modell ist ein **konzeptionelles ER-Modell**, kein fertiges Datenbankschema. Die Attributzahlen enthalten den jeweiligen Primärschlüssel. Zuordnungen zu anderen Entitäten stehen ausschließlich im Beziehungskatalog; technische Fremdschlüssel werden bei der relationalen Umsetzung daraus abgeleitet und hier nicht als zusätzliche fachliche Attribute doppelt aufgeführt. Abgeleitete Kennzahlen sind ebenfalls keine gespeicherten Attribute.

### 3.1 Entitäten: genau acht

| Nr. | Entität | Attribute einschließlich eindeutigem Schlüssel | Anzahl | Herkunft und Einordnung |
|---|---|---|---:|---|
| E1 | **Kunde** | **KundeID (PK)**, Name, E-Mail, Telefon | 4 | Wiederkehrende Auftraggeber und Kontaktdaten: M1-F10, M1-F11. Mehrere Ansprechpartner mit Rollen sind bewusst nicht in ein einziges Namensfeld gepresst, sondern außerhalb des V1-Kernmodells zurückgestellt. |
| E2 | **Event** | **EventID (PK)**, Bezeichnung, Eventdatum, Gesamtplanbudget, Locationangabe | 5 | M1-F01, M1-F04, M1-F05, M1-F07. Locationangabe ist zunächst ein Textwert, keine separat verwaltete Location. |
| E3 | **Dienstleister** | **DienstleisterID (PK)**, Name, Kontaktangabe, Erfahrungsstatus, Kontaktart | 5 | M1-F02, M1-F03, M2-F06. Erfahrungsstatus: `Stamm` oder `Neu`; Kontaktart: `regulär` oder `privat`. Diese Merkmale sind unabhängig voneinander. |
| E4 | **Gewerk** | **GewerkID (PK)**, Bezeichnung, Beschreibung | 3 | Zum Beispiel Catering, Technik, Personal: M1-F02, M1-F04, M2-F08. Eigener Katalog verhindert uneinheitliche Gruppierung. |
| E5 | **Buchung** | **BuchungID (PK)**, VereinbarterPreis, Buchungsstatus, Zahlungsfrist, Verbindlichkeitsgrund | 5 | Preis, Frist und Status: M1-F02, M2-F06, M2-F08. Verbindlichkeitsgrund ist eine Modellierungsannahme zur nachvollziehbaren Umsetzung der bestätigten Stamm-/Neu-Logik. |
| E6 | **Aufgabe** | **AufgabeID (PK)**, Titel, Fälligkeit, Bearbeitungsstatus, Zuständigkeit | 5 | M1-F12, M2-F04. Zuständigkeit wird zunächst als Text geführt; eine eigene Benutzerentität ist nicht Teil des Kernmodells. |
| E7 | **Gewerkbudget** | **GewerkbudgetID (PK)**, Planbetrag, Planungsnotiz | 3 | Geplante Aufteilung je Event und Gewerk: M1-F04. Eigene Zuordnungsentität ist eine Modellierungsannahme. |
| E8 | **Reservierungsnachweis** | **ReservierungsnachweisID (PK)**, KundenunterschriftAm, ErforderlicheAnzahlung, EingegangeneAnzahlung | 4 | Kundenunterschrift und Anzahlung als Voraussetzung: M1-F04, M1-F07, M2-F06. Minimaler manuell gepflegter Nachweis; ersetzt weder Rechnung noch Zahlungsbuchhaltung. |

Alle IDs sind stabil und eindeutig; Namen, Datum oder die Kombination aus Event und Dienstleister sind keine Primärschlüssel. Die Wahl technischer IDs ist eine Modellierungsannahme.

### 3.2 Beziehungen: genau acht

Die Minima und Maxima sind ausdrücklich angegeben. Die Kardinalitäten sind Modellierungsentscheidungen auf Grundlage der genannten Fälle; unbestätigte Mehrfachauftraggeber sind nicht vorausgesetzt.

| Nr. | Beziehung und Kardinalität | Bedeutung und Beleg |
|---|---|---|
| B1 | **Kunde 1 — 0..n Event** | Jedes Event gehört genau einem Kunden; ein Kunde kann mehrere Events haben. M1-F10; genau ein Auftraggeber je Event ist eine Modellierungsannahme. |
| B2 | **Event 1 — 0..n Buchung** | Jede Buchung gehört genau einem Event. Ein neu angelegtes Event kann zunächst ohne Buchung existieren. M1-F01, M2-F08. |
| B3 | **Dienstleister 1 — 0..n Buchung** | Jede Buchung bezeichnet genau einen Dienstleister. Derselbe Dienstleister kann mehrfach im selben Event und in verschiedenen Events gebucht werden. M1-F10, M2-F08. |
| B4 | **Gewerk 1 — 0..n Buchung** | Jede Buchung wird genau einem Gewerk zugeordnet. Mehrere Gewerke eines Dienstleisters werden in getrennte Buchungen zerlegt. Fachlich bestätigter Bedarf, gewählte Darstellung: M2-F08. |
| B5 | **Event 1 — 0..n Aufgabe** | Jede konkrete Aufgabe gehört genau einem Event. M1-F12, M2-F07. |
| B6 | **Event 1 — 0..n Gewerkbudget** | Jedes Gewerkbudget gilt für genau ein Event. M1-F04. |
| B7 | **Gewerk 1 — 0..n Gewerkbudget** | Jedes Gewerkbudget betrifft genau ein Gewerk. Pro Kombination Event/Gewerk ist höchstens ein Planansatz vorgesehen; Modellierungsannahme zur eindeutigen Aggregation. M1-F04. |
| B8 | **Event 1 — 0..1 Reservierungsnachweis** | Ein Event kann vor der Bestätigung noch keinen Nachweis besitzen. Jeder Nachweis gehört genau einem Event. Das Modell hält den aktuellen Stand, keine Zahlungshistorie. M1-F04, M2-F06. |

**n:m-Nachweis:** Event und Dienstleister stehen fachlich in einer n:m-Beziehung, aufgelöst durch **Buchung** über B2/B3. Ein Event hat mehrere Dienstleister; ein Dienstleister wirkt an mehreren Events mit. Zusätzlich erlaubt die eigene BuchungID mehrere Leistungszuordnungen für dasselbe Event-Dienstleister-Paar. Es gibt daher ausdrücklich **keine Eindeutigkeitsbedingung allein auf diesem Paar**, auch nicht auf Event/Dienstleister/Gewerk. Zwei getrennte Leistungen desselben Gewerks bleiben möglich. Event und Gewerk sind für ihre Planansätze ebenfalls n:m über Gewerkbudget verbunden. Diese Erläuterungen sind keine zusätzlichen Beziehungen neben B1–B8.

### 3.3 Status und Verbindlichkeit

**Bestätigt:** Dienstleisterbuchungen benötigen mindestens `angefragt`, `zugesagt`, `vertraglich bestätigt` (M2-F06). Die App zeigt den Status je Buchung, nicht nur einmal am Dienstleisterstammsatz.

**Modellierungsannahme:** `abgesagt` ergänzt die drei genannten Stufen, um den ausdrücklich berichteten Ausfall abzubilden (M1-F03, M1-F06). Eine Ersatzbuchung erhält eine neue BuchungID; die ausgefallene Buchung bleibt sichtbar. Ein Statuswechsel wird bewusst erfasst, nicht aus einem vermuteten E-Mail-Inhalt automatisch abgeleitet.

Der Verbindlichkeitsgrund unterscheidet `kein Nachweis`, `Stamm-Zusage per E-Mail` und `Vertragsunterschrift`. Damit bleibt bei der Buchung erkennbar, warum Sabine sie fest eingeplant hat. Eine spätere Änderung des Erfahrungsstatus eines Dienstleisters darf bestehende Zusagen nicht rückwirkend anders auslegen; die Speicherung des damaligen Grundes ist eine Modellierungsannahme. Die fachliche Entscheidungslogik ist in GR-03 festgelegt. Ein abgesagter Eintrag wird nicht als aktive verbindliche Buchung dargestellt, auch wenn der frühere Nachweis erhalten bleibt.

**Aufgabenstatus – Modellierungsannahme:** `offen`, `in Bearbeitung`, `blockiert`, `erledigt`. Das Interview fordert einen erkennbaren Fortschritt und nennt offene sowie blockierte Aufgaben, legt aber keinen vollständigen Statuskatalog fest (M1-F12, M2-F04). Überfälligkeit wird aus Fälligkeit und einem noch nicht erledigten Zustand abgeleitet. Zuständigkeit bedeutet Arbeitszuordnung, nicht automatisch ein Zugriffsrecht.

### 3.4 Budgetberechnung

**Bestätigt:** Gesamtplanbudget und geplante Kosten je Gewerk werden Buchungskosten gegenübergestellt (M1-F04, M2-F07, M2-F08). Das vereinbarte Entgelt und die Zahlungsfrist gehören zur einzelnen Buchung. Catering und Personal desselben Anbieters dürfen nicht zusammenfallen.

Für die Auswertung werden folgende abgeleitete Werte verwendet:

- `Gewerkssumme = Summe der einbezogenen Buchungspreise dieses Events und Gewerks`.
- `Buchungssumme = Summe aller Gewerkssummen des Events`.
- `Verbleibend = Gesamtplanbudget − Buchungssumme`.
- `Auslastung = Buchungssumme / Gesamtplanbudget × 100`, sofern das Budget größer als null ist; andernfalls wird kein Prozentwert ausgegeben.

**Modellierungsannahme zur Kostenauswahl:** Die verbindliche Summe enthält aktive verbindliche Buchungen gemäß GR-03. Daneben zeigt eine gesonderte Vorschau die Preise noch unverbindlicher Anfragen/Zusagen; abgesagte Buchungen werden nicht in die aktive Leistungssumme aufgenommen. Diese Trennung verhindert, dass parallele Ersatzanfragen unbemerkt als fest beauftragte Kosten erscheinen. Welche Summe Sabine als führende „Buchungssumme“ versteht, ist in O3 noch zu bestätigen. Mögliche Stornokosten sind damit nicht erledigt; deren Behandlung bleibt offen.

Die Ansicht weist auch Gewerke ohne Planansatz aus. Die Summe der Gewerkplanbeträge und deren Differenz zum Gesamtplanbudget werden sichtbar gemacht; eine erzwungene Gleichheit wurde nicht verlangt. Nettopreise, Bruttopreise und unterschiedliche Währungen dürfen nicht stillschweigend vermischt werden; die verbindliche Betragsbasis ist noch festzulegen (O2).

Die genannten **85 %** sind ein Interviewbeispiel für den Informationsbedarf, kein vereinbarter Warnschwellwert. Auch aus der Erfahrung mit **130 %** folgt keine automatische Buchungssperre. Ein Warnsystem und seine Schwellen sind noch nicht spezifiziert.

## 4. Geschäftsregeln: genau fünf

### GR-01 – Kundenreservierung setzt Unterschrift und Anzahlung voraus

Eine Kundenreservierung wird erst als verbindlich ausgewiesen, wenn die Kundenunterschrift vorliegt und die erforderliche Anzahlung eingegangen ist. Die Anzahlung beträgt 30 %; eine alleinige Angebotsannahme oder Anzahlungsrechnung genügt nicht. Die Restzahlung wird 14 Tage vor dem Event fällig.

**Belege:** M1-F04: „30% Anzahlung“, „ohne die … keine verbindliche Reservierung“, Restzahlung „14 Tage vor dem Eventdatum“; M1-F07: „Ohne Anzahlung blocke ich keinen Dienstleister, keine Location“; M2-F06: „ohne Unterschrift und ohne Anzahlung reserviere ich gar nichts“.

**Umsetzung/Prüfung:** Der Reservierungsnachweis hält Unterschrift und Zahlungsstand fest. Bei fehlender Unterschrift oder unvollständiger Anzahlung bleibt die Reservierung unverbindlich. Die Berechnungsbasis der 30 % ist offen (O1); sie darf nicht einfach mit dem internen Gesamtplanbudget gleichgesetzt werden. Die Restzahlungsregel wird in V1 dokumentiert, ihre Rechnungsabwicklung bleibt V2.

### GR-02 – Drei-Wochen-Frist mit dokumentiertem Ausfallfall

Im Normalfall sollen alle Dienstleister spätestens 21 Kalendertage vor dem Event final bestätigt sein. Fällt ein bereits eingeplanter Dienstleister kurzfristig aus, darf nach dieser Grenze Ersatz organisiert werden. Die Anwendung hält die ursprüngliche Absage und die separate Ersatzbuchung nachvollziehbar auseinander und blockiert die Rettung des Events nicht aufgrund der überschrittenen Normalfrist.

**Belege:** M1-F05: „Final-Bestätigung aller Dienstleister … spätestens drei Wochen vor dem Eventdatum“; M1-F06: „Drei-Wochen-Grenze gilt für den Normalfall“, nach Absage „Ausnahmesituation“.

**Umsetzung/Prüfung:** Finalbestätigung wird nach GR-03 beurteilt. Ein Ersatz zehn Tage vor dem Event bleibt erfassbar; eine noch ausstehende Bestätigung im Normalfall ist als Fristproblem erkennbar. Die Darstellung als Fristproblem ist eine Modellierungsannahme; ein konkreter Benachrichtigungskanal wurde nicht festgelegt.

### GR-03 – Dienstleisterverbindlichkeit hängt von Stamm-/Neu-Status ab

Bei Stamm-Dienstleistern genügt eine schriftliche E-Mail-Zusage für Sabines verbindliche Planung. Bei neuen Dienstleistern genügt diese Zusage nicht; sie werden erst mit Vertragsunterschrift verbindlich eingeplant. Eine rein telefonische Zusage reicht nicht als schriftlicher Nachweis. Diese Regel beschreibt Sabines betriebliche Praxis, keine juristische Bewertung.

**Belege:** M1-F06: „eine Mail reicht mir, ein Anruf reicht mir nicht“; M2-F06: Stamm-Dienstleister „Mail-Zusage als verbindliche Buchung“, neue Dienstleister „warte ich tatsächlich auf die Unterschrift“.

**Umsetzung/Prüfung:** Zwei Buchungen mit Status `zugesagt` können unterschiedlich verbindlich sein. Die Oberfläche zeigt Status und Verbindlichkeit getrennt. Der Ausnahmefall Ersatzbeschaffung hebt die spätere ausdrückliche Unterscheidung aus M2-F06 nicht automatisch auf; siehe W2 und O4.

### GR-04 – Preissteigerungen benötigen Sabines Freigabe; maßgeblich ist die bestätigte Angebotsversion

Erhöhen Änderungen die Summe der Buchungen, entscheidet Sabine persönlich über die Freigabe. Bei Angeboten gilt die letzte bestätigte Version als verbindlich; spätere unbestätigte Fassungen bleiben Entwürfe. Führt eine Angebotsänderung zu einer Preiserhöhung, greift ebenfalls ihre Freigabe.

**Belege:** M1-F04: Budgetanpassung „persönlich absegnen“, „delegiere ich nicht“; M1-F08: „Wenn die Summe meiner Buchungen nach oben geht, entscheide das ich“ sowie „Die letzte bestätigte Version ist verbindlich, alles andere ist Entwurf“.

**Umsetzung/Prüfung:** In V1 liegt die Kostenpflege bei Sabine; ein technischer Mehrpersonen-Freigabeprozess wird daraus nicht vorweggenommen. In V2 darf ein neuer Angebotsentwurf die bestätigte Fassung nicht automatisch ablösen. Das Kundinnenbudget und Sabines interne Kostenentscheidung sind getrennte Sachverhalte.

### GR-05 – Mehrere Gewerke benötigen getrennt auswertbare Buchungen

Übernimmt derselbe Dienstleister mehrere Gewerke für dasselbe Event, müssen Preis und Zahlungsfrist je Leistung getrennt erfasst werden können. Die Budgetansicht summiert die Kosten je Gewerk und insgesamt, unabhängig davon, ob mehrere Leistungen vom selben Anbieter kommen.

**Belege:** M1-F02: Zuordnung mit „Gewerk, vereinbarter Preis, Zahlungsfrist“; M2-F08: Thomas übernimmt Catering und Personal, „zwei verschiedene Rechnungen, manchmal zwei verschiedene Zahlungsfristen“ sowie „aufgeschlüsselt nach Gewerk“.

**Umsetzung/Prüfung:** Das Modell entscheidet sich für getrennte Buchungen mit jeweils einem Gewerk; eine zusätzliche Sammelbuchung mit Positionen ist nicht nötig. Zwei getrennte Rechnungen können in V2 daran angeschlossen werden. Ein Dienstleisterstammsatz erhält deshalb kein einziges festes Gewerk, das andere Leistungen ausschließt.

## 5. Explizit aufgelöste Widersprüche: genau zwei

### W1 – „Harte“ Drei-Wochen-Grenze gegenüber kurzfristiger Ersatzbeschaffung

**Gegensätzliche Aussagen:** M1-F05 nennt die Bestätigung spätestens drei Wochen vorher eine „harte Grenze“. M1-F03 schildert die Absage der Technikfirma genau drei Wochen vor einem Stadtfest und die anschließende kurzfristige Ersatzsuche.

**Interviewklärung:** Auf die direkte Rückfrage in M1-F06 unterscheidet Sabine den Normalfall von der Krisensituation nach einer Absage.

**Auflösung:** Die Frist bleibt der Normalmaßstab, ist aber keine absolute Eingabesperre. Ein Ausfall wird als Ausnahme behandelt, die Ersatzbuchung separat erfasst. Verbindlichkeit richtet sich zusätzlich nach GR-03. Diese Auflösung stammt aus dem Interview und ist keine frei erfundene Fristverlängerung.

### W2 – Schriftliche E-Mail-Zusage gegenüber „ohne Unterschrift nichts verbindlich“

**Gegensätzliche Aussagen:** M1-F06 erklärt die schriftliche Zusage per E-Mail beim Ersatzdienstleister für ausreichend. M2-F05 sagt dagegen pauschal: „Ohne Unterschrift ist nichts verbindlich.“

**Interviewklärung:** In M2-F06 benennt Sabine ausdrücklich zwei Standards: E-Mail genügt bei Stamm-Dienstleistern; bei neuen Dienstleistern wartet sie auf die Unterschrift. Beim Kunden verlangt sie Unterschrift und Anzahlung.

**Auflösung:** Die spätere, ausdrücklich differenzierte Erklärung ersetzt die pauschalen Aussagen als fachliche Grundlage. Dienstleisterstatus, Buchungsstatus und Verbindlichkeit werden getrennt betrachtet. Eine E-Mail-Zusage macht einen neuen Ersatzanbieter nicht automatisch verbindlich. Ob Sabine für diesen speziellen Krisenfall eine zusätzliche Ausnahme will, ist offen; sie wird nicht erfunden.

## 6. V1-Abläufe und Abnahmekriterien

| Fall | Erwartetes Verhalten | Grundlage |
|---|---|---|
| Wiederkehrender Kunde | Zwei Events können demselben Kunden zugeordnet werden, behalten aber eigene Buchungen, Budgets und Aufgaben. | M1-F10; B1–B7 |
| Thomas mit zwei Gewerken | Für ein Event werden Catering zu 8.000 EUR und Personal zu 2.000 EUR als zwei Buchungen desselben Dienstleisters erfasst. Unterschiedliche Zahlungsfristen bleiben erhalten. | M2-F08; GR-05 |
| Budget nach Gewerk | Bei 20.000 EUR Gesamtplanbudget und ausschließlich diesen zwei verbindlichen Buchungen zeigt die Ansicht 10.000 EUR, 50 %, 10.000 EUR verbleibend sowie Catering 8.000 EUR und Personal 2.000 EUR. | M1-F04, M2-F08; GR-05; Kostenauswahl als Annahme aus 3.4 |
| Stamm gegenüber neu | Bei schriftlicher Zusage ist ein Stamm-Dienstleister fest eingeplant; ein neuer Anbieter bleibt bis zur Unterschrift unverbindlich. Beide können den Status `zugesagt` haben. | M2-F06; GR-03 |
| Ausfall kurz vor dem Event | Die ausgefallene Technikbuchung wird als abgesagt erhalten. Zehn Tage vor dem Event lässt sich eine neue Ersatzbuchung anlegen. Die alte Leistung wird nicht nochmals in die aktive Buchungssumme gerechnet. | M1-F06; GR-02; Berechnungsannahme 3.4 |
| Reservierung | Bei erforderlicher Anzahlung von 3.000 EUR bleiben 2.999 EUR auch mit Unterschrift unzureichend. 3.000 EUR ohne Unterschrift reichen ebenfalls nicht; beides zusammen erfüllt die Voraussetzung. | M1-F04, M2-F06; GR-01 |
| Aufgabenübersicht | Eine überfällige offene Aufgabe „Catering-Briefing“ mit Zuständigkeit Sabine ist im richtigen Event sichtbar; eine erledigte Aufgabe wird nicht weiterhin als offen gezählt. | M1-F12, M2-F07; Statusmodell als Annahme |

Die Testbeträge legen weder eine Währungspolitik noch die noch offene Netto-/Bruttobasis fest. Abnahmefähig sind die Rechenbeispiele nach einheitlicher Festlegung dieser Basis.

## 7. Genau eine neue Anforderung aus Meeting 2: Eventvorlagen

**NA-01 – Wiederkehrende Events aus einem bewusst gepflegten Grundgerüst anlegen (V2).**

**Entstehung und Beleg:** In M2-F01, der Frage „Was planst du als Nächstes für Kleist Events?“, bringt Sabine erstmals Eventvorlagen auf. M2-F02 konkretisiert das Beispiel TechCorp-Weihnachtsfeier; M2-F03 erläutert die Trennung zwischen Vorlage und konkretem Event. M2-F07 priorisiert die Umsetzung für V2.

Eine Vorlage stellt Dienstleistervorschläge, Standardaufgaben und relevante Notizen bereit. Im konkreten Auftrag bleiben Datum, Location, Gästezahl, Motto und Sonderwünsche anpassbar. Sabine nennt beispielhaft Location-Begehung vier Monate vorher, Catering-Briefing sechs Wochen vorher, Finalbestätigung drei Wochen vorher und Anzahlungsrechnung nach Auftragsbestätigung. Die Erdnussunverträglichkeit des Geschäftsführers ist ein Beispiel für eine wichtige Notiz, keine Forderung nach einem eigenständigen Allergieverwaltungssystem.

Änderungen am abgeleiteten Event verändern die Vorlage nicht automatisch. Eine dauerhafte Änderung der Vorlage erfolgt ausdrücklich. Vorausgewählte Dienstleister sind Vorschläge, keine bereits verbindlichen Buchungen; ihre Verfügbarkeit muss für den konkreten Termin beurteilt werden. Der in M2-F03 angesprochene eventübergreifende Verfügbarkeitsblick konkretisiert zugleich das bereits in M1-F10 geschilderte Mehrfachbuchungsproblem.

**Abnahme in V2:** Ein aus einer Vorlage erzeugtes Event erhält die Vorschläge und Standardaufgaben. Wird dort der Fotograf ersetzt, bleibt die Vorlage unverändert. Ein weiterer Ableitungsversuch zeigt weiterhin den ursprünglichen Fotografen. Eine bewusst bearbeitete Vorlage kann anschließend den neuen Standard enthalten.

**Modellabgrenzung:** Das Modell in Abschnitt 3 beschreibt den V1-Kern einschließlich Budget und minimalem Reservierungsnachweis. Für V2 werden Vorlagenstrukturen separat entworfen; sie sind hier keine neunte Entität. Vorlagen werden nicht durch Wiederverwendung lebender Aufgaben- oder Buchungsdatensätze simuliert. Die genaue Speicherung und Berechnung relativer Termine ist noch zu entwerfen. Andere Erkenntnisse aus Meeting 2 werden als Präzisierungen oder Priorisierungen bestehender Themen dokumentiert, nicht als weitere nummerierte neue Anforderung.

## 8. Fakten, Annahmen und bewusste Grenzen

**Bestätigte Fakten:** Private Kontakte werden bei Hochzeiten insbesondere für Floristik und Torte genutzt; bei Firmenveranstaltungen arbeitet Sabine mit ihrer regulären Liste (M1-F02, M1-F03). Stamm-Dienstleister werden bei vergleichbarem Preis und vergleichbarer Qualität bevorzugt; kurzfristiger Ersatz kann neu sein. Daraus wird kein automatischer Auswahlalgorithmus und kein absolutes Verbot anderer Anbieter abgeleitet. Kontaktart und Erfahrungsstatus bleiben getrennte Informationen.

**Bestätigte Fakten:** Sabine benötigt auch Termine, verlässliche Locationangaben, Ansprechpartner mit unterschiedlichen Rollen sowie Informationen zu Verträgen, Rechnungen und Versionen (M1-F05, M1-F07–F12). Diese Themen verschwinden nicht durch die Begrenzung auf acht Entitäten. Das Modell setzt für V1 die drei ausdrücklich priorisierten Arbeitsbereiche um und hält den weitergehenden Bedarf in der Priorisierung und den offenen Fragen fest.

**Modellierungsannahmen:** Technische IDs; genau ein Kunde pro Event; ein Gewerk je Buchung; separate Gewerkplanansätze; Textzuständigkeit statt Benutzerverwaltung; Statuskatalog für Aufgaben; gespeicherter Verbindlichkeitsgrund; eine aktuelle Reservierungsnachweiszeile; explizite Trennung verbindlicher und unverbindlicher Kostensummen. Diese Entscheidungen sind überprüfbare Entwurfsentscheidungen, keine angeblich wörtlichen Interviewforderungen.

**Bewusste Grenzen:** V1 enthält keine automatische E-Mail-Auswertung, keine automatische Anbieterbuchung, keine behauptete Lexware-Schnittstelle und keine erfundenen Warnschwellen. Eine Datumsüberschneidung belegt außerdem nicht automatisch die vollständige Nichtverfügbarkeit eines Dienstleisters: Kapazitäten wurden nicht erhoben. V2-Rechnungsdaten dürfen später nicht zusätzlich zum zugehörigen Buchungspreis als zweite Leistung in dieselbe Budgetsumme eingehen; das konkrete Rechnungsmodell ist noch auszuarbeiten.

## 9. Offene Fragen

| ID | Zu klärender Punkt | Auslöser / Bedeutung |
|---|---|---|
| O1 | Auf welchen verbindlichen Kundenbetrag beziehen sich die 30 %; was passiert bei späteren Preisänderungen und Teilzahlungen? | M1-F04, M1-F07, M2-F06. Der Kundenpreis ist nicht automatisch das interne Kostenbudget. V1 kann den erforderlichen Betrag zunächst manuell dokumentieren. |
| O2 | Werden Budget und Buchungspreise netto oder brutto geführt? Welche Währung gilt? | M1-F09 nennt Netto und Mehrwertsteuer auf Rechnungen, legt die Basis des Budgetvergleichs aber nicht fest. Vor rechnerischer Abnahme klären. |
| O3 | Welche Status fließen in die führende Buchungssumme ein? Wie werden Alternativangebote, Stornogebühren und nachträgliche Preisänderungen behandelt? | M1-F04–F06, M2-F08. Die Trennung in 3.4 ist ein Vorschlag, noch keine bestätigte Detailregel. |
| O4 | Gilt für einen neuen Ersatzanbieter im Krisenfall weiterhin zwingend die Unterschrift? | M1-F06 gegenüber der späteren Differenzierung M2-F06. Bis zur Klärung gilt die spezifischere Erklärung aus M2-F06. |
| O5 | Wer und anhand welcher Kriterien stuft einen neuen Dienstleister als Stamm ein? | M1-F03, M2-F06. Das Merkmal beeinflusst die Verbindlichkeit; keine automatische Hochstufung ableiten. |
| O6 | Welche Aufgabenstatus und Pflichtangaben wünscht Sabine genau; braucht sie Erinnerungen und wenn ja über welchen Kanal? | M1-F12, M2-F04. Statuswerte und Fristdarstellung sind bisher Modellierungsannahmen. |
| O7 | Wann werden Verfügbarkeitsübersicht, Termin-/Locationverwaltung und mehrere Ansprechpartner umgesetzt? Welche Kapazitäten/Zeitintervalle sind relevant? | M1-F05, M1-F10, M1-F11, M2-F03. Ein einzelnes Eventdatum reicht nicht für eine belastbare Kapazitätsprüfung. |
| O8 | Welche Dokumente, Versionen und Zahlungsinformationen soll V2 selbst speichern, welche nur aus Lexware oder Ablagen referenzieren? | M1-F07–F09, M2-F05, M2-F07. Umfang und Schnittstellen sind nicht vereinbart. |
| O9 | Wie werden Vorlagen bewusst gepflegt und relative Aufgabenfristen berechnet? Wie werden sensible Notizen und V2-Zugriffe abgegrenzt? | M2-F02–F04. Monatsabstände und Zugriffsgrenzen benötigen weitere Detailentscheidungen. |
| O10 | Gibt es zusätzliche Bewertungskriterien oder ein vorgeschriebenes Anforderungsmodell aus dem Kurs? | Kursdateien sind nicht zugänglich. Die Spezifikation kann nur gegen die im Auftrag genannten Vorgaben geprüft werden. |

## 10. Interviewindex und Rückverfolgbarkeit

Die Kurzbeschreibungen sind sinngemäße Inhaltsangaben; Anführungszeichen in den Geschäftsregeln kennzeichnen kurze Originalauszüge. Die Frageanfänge dienen als Suchanker in der vollständigen Transkription.

| Referenz | Frage / Suchanker | Relevanter Inhalt der Antwort |
|---|---|---|
| M1-F01 | „Wie lief bei dir bisher die Planung …?“ | Erstgespräch, Angebot, Excel pro Event, Dienstleisteranfragen, Kosten/Fristen und getrennte Kalenderinformationen. |
| M1-F02 | „… Dienstleister ausgewählt, ihre Zusagen festgehalten …?“ | Stammkontakte, E-Mail-Zusagen, Gewerk/Preis/Zahlungsfrist; private Hochzeitskontakte außerhalb der regulären Liste. |
| M1-F03 | „… bei anderen Veranstaltungen wie Jubiläen …?“ | Zuordnung von Jubiläen, Bevorzugung bewährter Anbieter, kurzfristige Absage beim Stadtfest in Spandau und neuer Ersatz. |
| M1-F04 | „… Budget geplant … Zahlungen organisiert …?“ | Ca. 35.000 EUR Budget beim Produktlaunch, Gewerkeplanung, 30 % Anzahlung, Restzahlung 14 Tage vorher, 130-%-Erfahrung, persönliche Budgetfreigabe. |
| M1-F05 | „… Termine und Locations … koordiniert …?“ | Outlook ohne Eventverknüpfung, Terminkollisionen, veraltete Locationadresse, Finalbestätigung drei Wochen vor dem Event. |
| M1-F06 | „… feste Frist … Ersatzdienstleister … verbindlich …?“ | Normalfall gegenüber Ausfallausnahme, schriftliche Zusage statt Telefonat, Bedarf an Status-/Fristübersicht und Risiko paralleler Anfragen. |
| M1-F07 | „… Informationen in diesem Angebot … Änderungen …?“ | Angebot mit Datum, Location, Gästezahl und Kosten je Gewerk, 30 % Anzahlung, Änderungen und unklare Versionen. |
| M1-F08 | „… Budgetänderungen … keine feste Regel …?“ | Sabines Entscheidung über steigende Buchungssumme; letzte bestätigte Angebotsversion gilt, andere bleiben Entwurf. |
| M1-F09 | „… Rechnungen und Zahlungen … verwaltet …?“ | Mann übernimmt Buchhaltung/Lexware, Netto/MwSt./Datum/Zahlungsziel, Eventzuordnung, Mahnungen und fehlende gemeinsame Übersicht. |
| M1-F10 | „… Kunde mehrere Events … derselbe Dienstleister …?“ | Ein Kunde mit mehreren Events, Anbieter für mehrere Events, übersehene Doppelzusage eines Fotografen. |
| M1-F11 | „… Ansprechpartner … mehrere Personen …?“ | Kontaktdaten in Outlook, verschiedene Rollen, Informationsverlust beim Wechsel der Ansprechpartner. |
| M1-F12 | „… noch etwas Wichtiges … übersehen …?“ | Vertragszuordnung und Unterschriftenstand; Aufgaben je Event mit Frist, Delegation, Offen-/Blockiert-Zustand. |
| M2-F01 | „Was planst du als Nächstes für Kleist Events?“ | Strukturierter arbeiten; erstmals Vorlagen für wiederkehrende Events mit Dienstleistervorschlägen und Standardaufgaben. |
| M2-F02 | „… welche Informationen … in einer solchen Vorlage …?“ | TechCorp-Beispiel, Anbieter, Standardaufgaben mit relativen Fristen, variable Eventdaten und wichtige Notizen. |
| M2-F03 | „… Änderung … konkrete Event … Vorlage …?“ | Keine automatische Rückwirkung, bewusste Vorlagenpflege, Austausch nicht verfügbarer Anbieter, eventübergreifender Verfügbarkeitsblick. |
| M2-F04 | „… wer welche Aufgaben … einsehen oder ändern …?“ | Sabine operativ, Mann Buchhaltung, Tochter vor Ort; unterschiedliche Informations- und Zugriffsbedarfe. |
| M2-F05 | „… wann diese Verträge erstellt und unterschrieben …?“ | Kundenvertrag und Anzahlungsrechnung nach Annahme; Unterzeichner/Datum/Event; pauschale Unterschriftsforderung; Nachträge/Versionen. |
| M2-F06 | „… E-Mail ausreicht … ohne unterschriebenen Vertrag …?“ | Differenzierung Stamm/Neu/Kunde; mindestens angefragt, zugesagt, vertraglich bestätigt. |
| M2-F07 | „… erste Version nur drei Bereiche …?“ | V1: Event/Buchungen, Budget, Aufgaben. V2: Verträge/Rechnungen, Vorlagen, Zugriffsrechte. |
| M2-F08 | „… konkreten Fall … Grenzen stoßen …?“ | Mehrere Gewerke je Anbieter/Event, getrennte Preise/Rechnungen/Fristen, Budgetaufschlüsselung je Gewerk. |

## 11. Formale Prüfung

| Vorgabe | Ergebnis |
|---|---|
| 6–8 Entitäten | **8**, E1–E8 |
| Eindeutige Schlüssel, jeweils 3–5 Attribute | **Erfüllt im konzeptionellen Modell**, einschließlich PK; Attribute je Entität: 4 / 5 / 5 / 3 / 5 / 5 / 3 / 4 |
| 6–9 Beziehungen mit Kardinalitäten | **8**, B1–B8, jeweils mit Minimum und Maximum |
| Mindestens eine n:m-Beziehung | **Event ↔ Dienstleister über Buchung**, zusätzlich Event ↔ Gewerk über Gewerkbudget |
| Genau fünf belegte Geschäftsregeln | **GR-01–GR-05**, jeweils mit präzisen Interviewreferenzen |
| Genau zwei explizit aufgelöste Widersprüche | **W1 und W2**, jeweils Ausgangsaussagen, Interviewklärung und Konsequenz |
| Genau eine neue Anforderung aus Meeting 2 | **NA-01 Eventvorlagen**, V2 |
| V1/V2 und offene Fragen | Abschnitte 2.1 und 9 |
| Fakten gegenüber Annahmen unterscheidbar | Kennzeichnung in Abschnitten 1, 3–8; keine behaupteten Inhalte nicht zugänglicher Kursunterlagen |
