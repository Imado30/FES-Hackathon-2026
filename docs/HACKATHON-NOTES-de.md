> Original development notes from the hackathon (German). The project overview is in the [README](../README.md).

# Team 01 – Frankfurt Impact Challenge (FES Hackathon 2026)

**GeMainsam** ist unsere App: eine Anwendung für Frankfurt, die nachhaltiges Verhalten sichtbar macht und belohnt.
Ein Chamäleon (Leon) führt durch Bus und Bahn (Transdev/traffiQ), Mehrweg (Vytal), Lebensmittelrettung
(foodsharing) und Sauberkeit (FES). Der lauffähige Code liegt in **`apps/mobile/`**.

## Verbindung in Expo Go

`npm run dev:lan` in `apps/mobile` startet Backend und Expo zusammen und verwendet dieselbe private LAN-Adresse für beide. Der Start wählt physische Netzwerkanschlüsse vor VPN-Adressen. Mac und Handy bleiben im selben Hotspot/WLAN; nach einem Netzwerkwechsel den gemeinsamen Startbefehl neu starten und den neuen QR scannen. iOS muss Expo Go Zugriff auf das lokale Netzwerk erlauben. Es wird kein öffentlicher Tunnel geöffnet.

Expo SDK 57 meldet Netzfehler als `FetchError`, nicht nur als `TypeError`. Die App behandelt beide, behält bei Verbindungsfehlern die Sitzung und versucht Leseanfragen einmal erneut. Schreibanfragen werden nie automatisch wiederholt: Nach einem Antwortabbruch ist der Speicherstatus unklar und muss zuerst durch Neuladen geprüft werden. „Erneut verbinden“ prüft jetzt eine tatsächliche Serverantwort. Auch die KI-Erfassung zeigt bei Netzwerkfehlern eine verständliche Meldung; die manuelle Eingabe bleibt verfügbar. Dauerhafte WLAN-/Internetausfälle kann der Client nicht verhindern.

## Aktueller Stand: Verkehrsplanung, QR-Nachweise und Feedback

- **traffiQ-Daten:** „Impact“ oder „Gemeinsam“ → „Verkehrsdaten“. Die neue Ansicht zeigt Fahrgastzahlen, Auslastung je Halt, Spitzen und zeitgewichtete Kapazitätsnutzung je Fahrt. Originale U7-Zählungen und simulierte Fahrten bleiben getrennt. Tagesgang und Haltestellenanfragen sind Nachfrageindikatoren, keine gezählten Fahrgäste. Für Bus/Straßenbahn fehlen entsprechende AFZ-Zählungen; aus der kleinen Stichprobe folgt keine belastbare Streichung einer Linie.
- **Vytal:** Scannen oder Code eingeben, fertig. Jeder Behälter hat serverweit höchstens eine offene Ausleihe, auch beim erneuten Scan durch dieselbe Person. Die Rückgabeoption wurde aus der Nutzeroberfläche entfernt. Schadens-/Verlustmeldungen bleiben verfügbar. Historische Rückgabebelege und der geschützte Händleradapter bleiben für bestehende Daten erhalten; echte Vytal-Konten sind weiterhin nicht angebunden.
- **Foodsharing:** Verteiler/Saver zeigt den kurz gültigen Übergabe-QR oder vier Ziffern, die abholende Person scannt/bestätigt den Empfang. Der statische Regal-QR öffnet den Ort mit Foto, Audio und manueller Erfassung, erzeugt allein aber keine Punkte. Die Seite „Regalmeldungen & Betreuung“ ist entfernt. Alte persönliche Abholcodes müssen nach dem Update neu ausgestellt werden.
- **FES-Aktionen:** „Mitmachen“ merkt nur die Anmeldung. Zwei Personen am Aktionsort tauschen einen zwei Minuten gültigen, einmaligen QR aus. Der Server prüft getrennte Konten, frische Standorte, Gebiet und Zeitfenster. Punkte höchstens einmal je Person/Aktion. Eigene QR-Codes, Wiederholungen, falscher Ort und abgelaufene Codes geben keine neuen Punkte. Geteilte Aktions-QRs können auch auf einem zweiten Gerät über „Scannen“ geöffnet werden. Gäste erhalten einen Beleg ohne einlösbare Punkte. Geräte-Standorte und gegenseitige Bestätigung sind Plausibilitätsnachweise, keine externe FES-Bestätigung.
- **Feedback:** Ein gemeinsames Fenster für alle Bereiche, vorbereitete Videos ohne vorgeschaltetes Konfetti, animierte Aktionspunkte und Kontostand, lesbare Schaltflächen und immer „Schließen“. Kein automatisches Verschwinden. Leons Weltgrafik ist vollständig und unbewegt.
- **Entfernt:** FES-Behälter auch aus Karte/Liste, Journal-Seite und die Prämie „Gratis-Ausleihe Vytal“. Standortberechtigung bleibt auf die Nutzung der App beschränkt.

**Nach dem Update Expo und Trust-Server neu starten:** `cd apps/mobile` → `npm run dev:lan`. Konten und Serverbelege bleiben erhalten. Alte lokale Sofortpunkte für FES-Anmeldungen/Teilnahmen werden ohne Servernachweis nicht mehr als gültige Punkte gerechnet.

## Neu: ein Einstieg, Gastmodus und kurze Übergabecodes

Nach dem Tutorial: **Benutzername, E-Mail und Passwort** für einen gemeinsamen GeMainsam-Zugang oder **Ohne Anmeldung weiter**. Innerhalb von Foodsharing und Mehrweg gibt es keine weiteren Passwortformulare. Bestehende Serverkonten funktionieren weiter mit Benutzername und Passwort; E-Mail ist bei neuen Registrierungen erforderlich und bleibt privat. Gastmeldungen/Zusagen bleiben beim späteren Einrichten eines Kontos erhalten.

- **Orts-QR:** Regal, Angebot oder Restaurant direkt unter „Scannen → Regal, Verteiler oder Restaurant scannen“ öffnen. Der Aufkleber gibt allein keine Punkte.
- **Übergabe:** Persönlichen QR zeigen oder vier Ziffern nennen. Der Code gilt nur für die ausgewählte Aktion, verfällt nach wenigen Minuten und kann nur vom berechtigten Gegenüber eingelöst werden. Fünf falsche PIN-Versuche sperren diese PIN-Prüfung; die Zähler überleben Neustarts.
- **Regal:** Foto, Audio oder manueller Eintrag bleiben verfügbar, auch als Gast. Öffentliche Eigenmeldungen geben keine bestätigten Punkte; wiederholtes Scannen eines Regal-QR gibt keine Punkte.
- **Gäste:** Essen teilen/abholen und Belege nutzen ohne Registrierung. Gastübergaben erzeugen für beide Seiten keine einlösbaren Punkte; sonst ließen sich mit immer neuen Gastzugängen Punkte sammeln. Dies ersetzt weder Identitätsprüfung noch eine Sperre gegen abgesprochene Täuschungen.
- **Video:** Der Expo-Video-Hook übernimmt die Freigabe. Die App ruft beim Unmount kein `pause()` auf einem bereits freigegebenen Player mehr auf; die vier Clips werden vorbereitet und beim Öffnen des Feedbackfensters direkt abgespielt.

Ein gemeinsamer Startbefehl (alte Expo-/Server-Terminals zuvor mit **Ctrl+C** stoppen):

```bash
cd apps/mobile
npm run dev:lan
```

Das startet den lokalen Server und Expo gemeinsam. Beide werden mit Ctrl+C beendet. Das Handy bleibt im selben privaten WLAN/Hotspot. Alternativ funktionieren weiterhin die zwei Terminals unten. Nach Änderungen am Server **auch den Server neu starten**, Expo-Neuladen allein genügt nicht.

[Einrichtung der Regalbetreuung, QR-Formate und Testgrenzen](./EINSTIEG-UND-QR-UEBERGABEN.md).

## Gemeinsamer App-Stand · 12. September 2026

Der Main-Stand mit neuem Profil, Onboarding, Impact-/Frankfurt-Ansichten, Mobilitätsexport und Müll-Fotonachweis ist mit den Foodsharing- und Mehrweg-Änderungen aus `imad` zusammengeführt. Die Übergaben, Partnerseite und der bisherige Aktionsüberblick bleiben erreichbar. Die Navigation übernimmt die einheitliche dunkelblaue Gestaltung aus Main und die begrenzte Animation samt zuverlässiger Web-Bedienung aus imad.

- **Foodsharing:** Foto, Audio oder Texteingabe; Posten und Portionszahlen, freie Termine, Zusagen, persönlicher Abhol-QR und Bewertungen. Offene Regale zeigen gemeinsame Momentaufnahmen.
- **Mehrweg:** Ausleihe erfassen und Schaden melden. Rückgabe ist keine Nutzeroption mehr; bestehende Belege bleiben im Server erhalten.
- **FES:** Vorher-/Nachher-Fotos mit Zeitfenster, frischem Standort je Foto und Dublettenprüfung; keine Punkte je Müllstück. Standort-/Kamerafehler bleiben sichtbar und erzeugen keinen Nachweis.
- **Mobilität und Wirkung:** Fahrtansicht aus Main, Wochenfortschritt, Globus und Datenexport bleiben enthalten. Die mobile Punkteberechnung bleibt zentral.

## App starten: zwei Terminals

Voraussetzungen: **Node 22.18 oder neuer**, npm und Expo Go mit Unterstützung für **SDK 57**. Nach dem Pull einmal die festgeschriebenen Pakete installieren. Terminal 1 im Repository:

```bash
cd apps/mobile
npm ci --include=optional
npm run trust:lan
```

Terminal 2 ebenfalls vom Repository aus:

```bash
cd apps/mobile
npm run start:lan
```

Mac und Handy müssen im selben privaten WLAN oder persönlichen Hotspot sein. In den iPhone-Einstellungen für Expo Go **Lokales Netzwerk** erlauben. Den neuen QR-Code mit der iPhone-Kamera scannen und in Expo Go öffnen; den einmaligen Entwicklerhinweis mit **Continue** schließen. Beide Terminals und den Rechner laufen lassen. Öffentliches WLAN kann direkte Geräteverbindungen blockieren. Für diesen Stand ist kein öffentlicher Tunnel eingerichtet.

**Nur `npx expo start` reicht für gemeinsame Übergaben, Ausleihen und FES-Nachweise nicht:** Dafür muss der Server auf Port 8787 erreichbar sein. Die App findet ihn in der Entwicklung über die private Expo-/Metro-Adresse. `EXPO_PUBLIC_TRUST_URL` kann die Serveradresse ausdrücklich setzen; veröffentlichte Builds benötigen einen HTTPS-Endpunkt.

Die [App-Anleitung](../apps/mobile/README.md) beschreibt Mikrofonrechte, KI und Startfehler. Bestehende Schlüssel in `apps/mobile/.env` nicht überschreiben oder committen. Die nativen Paketversionen nicht unabhängig vom Expo-SDK aktualisieren.

## Zugänge, Ladenfreigabe und bestehende Daten

Die Anmeldung am App-Einstieg authentifiziert den **GeMainsam-Serverzugang**. Das zusätzliche Formular „Meine Daten“ bleibt ein lokales Profilformular und ändert keine Server-Zugangsdaten. Gäste erhalten eine pseudonyme Geräte-Sitzung ohne E-Mail oder Passwort. Derselbe Zugang gilt für Foodsharing und Mehrweg. **Ausloggen** im Profil beendet jetzt auch diese Serversitzung und entfernt deren Anzeigecache. „Lokale Daten löschen“ löscht keine Konten oder Belege auf dem Server.

Ein Ladenkonto muss einmal vom Betreiber einem Store zugeordnet werden. Ohne Freigabe kann es keine Rückgabe-QRs ausstellen. [Einrichtung und genaue Befehle](../apps/trust-server/README.md#einmalige-freigabe-einer-lokalen-rücknahmestelle).

Die bestehende Datenbank `apps/trust-server/data/trust.sqlite` behalten. Schema-Erweiterungen erfolgen additiv; Konten, Zusagen und bestätigte Belege werden nicht neu angelegt oder gelöscht. Die App ergänzt neue Profil-/Fotozustände beim Laden; bestätigte Serverbelege bleiben erhalten. Alte Selbstgutschriften bleiben unbestätigt.

## Prüfung vor weiteren Merges

```bash
cd apps/mobile
npm run typecheck
npm run trust:test
npm run test:proofs
node tools/check-ai.cjs
npx expo export --platform ios --platform android --platform web
```

Die Tests verwenden isolierte Datenbanken und synthetische Konten. `trust:test` deckt Rollen, Zusagen, Bestand, Bewertungen, Händlerfreigaben und einmalige Belege ab; `test:proofs` prüft Zeit, Ort, Dubletten und ungültige Fotonachweise. Der KI-Adaptertest sendet ohne `--live` keine echte KI-Anfrage. Prüfprotokoll und Merge-Entscheidungen: [Main-/imad-Zusammenführung](./MAIN-IMAD-INTEGRATION-2026-09-12.md).

**Grenzen:** GeMainsam-Rückgaben beenden noch keine echte Vytal-Leihfrist; der autorisierte Händleradapter fehlt. Müllfotos werden lokal plausibilisiert, nicht von FES bestätigt. Karten-/Verkehrs-Fallbacks und historische Angebote bleiben Demo-/Beispieldaten. Exporte und Browserprüfungen ersetzen keinen Kamera-, Mikrofon- und GPS-Test auf einem echten Handy.

## Wo was liegt

| Ordner | Inhalt |
| --- | --- |
| `apps/trust-server/` | Persistenter lokaler Server für Foodsharing, Mehrweg und Bewertungen |
| `apps/mobile/` | Die App (Expo / React Native, iOS + Android + Web). Eigene README mit Details |
| `konzept/` | Review und Entscheidungen, Master-Prompt, Punktemodell und Anti-Fehlanreiz-Regeln |
| `docs/` | Status, Verträge, Anforderungen, Quellenprüfungen der Vorarbeit |
| `Foodsharing API/`, `Mobilitätsdaten/` | Partnerdokumentation und Rohdaten des Veranstalters |
| `shared/`, `tests/`, `tools/`, `START.ps1` | Ältere Python-Integrationsdemo der Vorarbeit, nicht die App |

Die Punkte- und Fairnessregeln stehen in [`konzept/02-PUNKTE-UND-ANTI-FEHLANREIZ.md`](../konzept/02-PUNKTE-UND-ANTI-FEHLANREIZ.md).
Arbeitsregeln für das Repo und die Bewertungskriterien der Jury stehen in [`CLAUDE.md`](../CLAUDE.md).

## Ältere Integrationsdemo (Python)

Aus der Vorarbeit liegt zusätzlich eine lokale Python-Demo im Repo. Sie ist **nicht** die App und wird für den
Pitch nicht gebraucht. Start unter PowerShell mit `.\START.ps1`, danach <http://127.0.0.1:8765>.
Details und Grenzen: [Status und Tests](./STATUS_UND_TESTS.md), [Entscheidungen](./ENTSCHEIDUNGEN.md),
[Anforderungen](./ANFORDERUNGEN.md), [Verträge](./VERTRAEGE.md), [Rewards und Wirkung](./REWARDS_UND_WIRKUNG.md),
[UX und Demo](./UX_UND_DEMO.md).
