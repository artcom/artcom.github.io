---
layout: post
title: 'The case for Continuous Delivery & Integration'
author: <a href="https://github.com/????/">Julian Krumow</a>
excerpt_separator: <!--end-of-excerpt-->
---
## Prinzip

Continuous Delivery bedeutet, wiederkehrende und komplexe Vorgänge in der Softwareproduktion zu automatisieren, da eine Maschine diese Aufgaben besser erledigen kann. Das Ergebnis ist konstante Qualität der Software. Es müssen keine Entwickler mehr diese Aufgaben von Hand erledigen, was ermüdend wirken kann und dadurch Flüchtigkeitsfehler entstehen.
<!--end-of-excerpt-->

## Automatisierbare Aufgaben können hierbei sein

- testen der Anwendung oder Teile davon auf korrekte Funktion
- befüllen einer Anwendung mit neuen Inhalten
- zentrale Verwaltung verschlüsselter, sicherheitsrelevanter Informationen
  (Zertifikate, Passwörter, IDs)
- signieren der Anwendung mit oben genannten Informationen
- erzeugen einer Installationsdatei die an den Kunden oder im Hause
  ausgeliefert werden kann

## Fehler die dadurch verhindert werden können

- Abstürze duch fehlerhafte Installationen beim Kunden oder auf Demosystemen im
  Hause
- fehlende Features und Inhalte in der Anwendung
- fehlerhafte Signaturen und Ids
- versehentliche Installation der falschen Version einer Anwendung
- überstrapazieren von Mitarbeitern mit monotonen Aufgaben, die Nachlässigkeit und Unaufmerksamkeit zu Folge haben können

## Fallbeispiele

### In einem Kundenprojekt muss ein Backend System geliefert werden

Beispiele: Volkswagen, BMW, Deutsche Bank

Automatisierte Prozesse:

- automatisiertes testen des Programmcodes
- erstellen des Installationspaketes
- installieren auf Demo-Server
- befüllen der Demoversion mit Inhalten
- anlegen von Demo-Usern
- bereitstellen des Installationpaketes für Produktion

Manuelle Prozesse:

- installieren auf Produktionssystem des Kunden
- einrichten des Produktionssystems bei Kunden

| Modus | Mensch | Maschine |
|:----- |:------ |:-------- |
| manuell | ?? | -- |
| automatisch | ?? | 3min |

### Kunde benötigt eine App die er seinen Mitarbeitern firmenintern zur Verfügung stellen will

Beispiele: ACShell, Reiloy iPad App, Birds

Automatisierte Prozesse:

- automatisiertes testen des Programmcodes
- erstellen des Installationspaketes
- signieren mit dem internen Provisionierungs-Zertifikat der beteffenden Firma

Manuelle Prozesse:

- versenden einer Email an den Kunden (Automatisierung möglich)
- bereitstellen des Paketes via Upload auf Fileserver oder `owncloud.artcom.de` (Automatisierung möglich)

| Zeitaufwand | Mensch | Maschine |
|:----- |:------ |:-------- |
| manuell | 30min | -- |
| automatisch | 5min | 2,5min |

### Eine App soll für den App Store produziert werden

Beispiele: MeMobility, Birds

Die App muss sowohl für interne Zwecke als auch für eine Veröffentlichung in den App Store bereitgestellt werden. Zusätzlich müssen sich die beiden Applikationen optisch voneinander unterscheiden und nebeneinander installierbar sein. Die Testversion muss über ein Webportal installierbar sein.

Automatisierte Prozesse:

- automatisiertes testen des Programmcodes
- erstellen des Installationspaketes zum internen Gebrauch
- signieren des Paketes mit internem Provisionierungs-Zertifikat
- upload des Paketes auf Webportal
- erstellen des Installationspaketes für den App Store
- signieren des Store-Paketes mit Provisionierungs-Zertifikat für Store
- automatischer upload in den Store
- benachrichtigung der Tester dass eine neue Testversion erhältlich ist
- zusammenfassen aller Änderungen seit der letzten Version (changelog) für Tester

Manuelle Prozesse:

- bereitstellen des fertigen Paketes für manuellen upload in store (Automatisierung möglich)
- aktualisieren der Store Informationen (Screenshots, Texte) (Automatisierung möglich)

| Zeitaufwand | Mensch | Maschine |
|:----- |:------ |:-------- |
| manuell | 2h | -- |
| automatisch | 30min | 8min |



## Momentan auf Jenkins gebaute Projekte

| Projekt | Applikation | Features | Dauer eines Deliveryjobs |
|:------- |:----------- |:-------- |:----------- |
| ACShell | macOS App | * testen des Programmcodes<br> * erstellen von Testreports<br> * package erzeugen<br> * DMG Installer erzeugen<br> * Bereitstellung via Server | 1min |
| Birds | Exponate | * erstellen der Exponate<br> * deployen der Exponate auf Stagingsystem | 2min |
| | Android | * erstellen der Betaversion<br> * upload der Betaversion auf HockeyApp<br> * benachrichtigen der Tester<br> * aufbereiten der changelogs für Tester<br> | 3min |
| Deutsche Bank<br>Centerboard | CMS | * testen des Programmcodes<br> * erstellen von Testreports | 50sec |
| EffFeu | Backend | * testen des Programmcodes<br> * erstellen von Testreports<br> * installieren auf Demosystem<br> * installieren auf Produktionssystem | 50sec |
| InnoZ | iPin Demonstrator | * testen des Programmcodes<br> * erstellen von Testreports | k.a. |
| MeMobility | Backend | * testen des Programmcodes<br> * erstellen von Testreports | akkumuliert aus 10 Jobs: 20min |
| | Android | * testen des Programmcodes<br> * erstellen von Testreports<br>  * erstellen der Betaversion<br>  * upload der Betaversion auf HockeyApp<br> * benachrichtigen der Tester<br> * aufbereiten der changelogs für Tester<br> * erstellen der Produktionsversion<br>  * signieren der Produktionsversion<br> * archivieren der Produktionsversion auf HockeyApp<br>  * upload in den Google Play Store | akkumuliert aus 4 Jobs: 8min |
| | iOS | * testen des Programmcodes<br> * erstellen von Testreports<br>  * erstellen der Betaversion<br> * signieren der Betaversion<br> * upload der Betaversion auf HockeyApp<br> * benachrichtigen der Tester<br> * aufbereiten der changelogs für Tester<br> * erstellen der Produktionsversion<br> * signieren der Produktionsversion<br> * archivieren der Produktionsversion auf HockeyApp | akkumuliert aus 4 Jobs: 8min |
| Reifenhäuser | Reiloy iPad App | * testen des Programmcodes<br> * erstellen von Testreports<br>  * erstellen der Produktionsversion<br> * signieren der Produktionsversion<br> * bereitstellen der Produktionsversion via Server | 2,5min |
| SintRaM | Backend<br>Client | * testen des Programmcodes<br> * erstellen von Testreports | 30 sec |
| UHCI Room Control | Backend | * testen des Programmcodes<br> * erstellen von Testreports<br> * archivieren der Applikation auf Buildserver | 20sec |
|  | Web Client | * testen des Programmcodes<br> * erstellen von Testreports<br> * archivieren der Applikation auf Buildserver | 5min |
|  | Deployment | * entnahme der Archive aus o.g. Builds<br> * erstellen eins Installationspaketes <br> * archivieren des Installationspaketes auf Buildserver | 4sec |
| Volkswagen | Pankow | * testen des Programmcodes<br> * erstellen von Testreports<br> * erstellen eins Installationspaketes <br> * deployment auf internem Stagingsystem | 3min |

-------

TODO: mögliche weitere Fallbeispiele:

* Kunde braucht neue Softwareversion mit korrigierten Inhalten
* Es wurde beim Kunden vor Ort ein Fehler gefunden, Kollegen stehen unter Zeitdruck und können die Anwendung deshalb nicht schnell genug von Hand erstellen

- Kundenprojekte
    - plattformen
    - projekt teile
    - applikationen
    - besonderheiten, vorteile
- Inhouseprojekte
    - travis @ github
    - oft benutzte Libraries -> Qualitätssicherung, Basis für Kundenprojekte
