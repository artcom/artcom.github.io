---
layout: post
title: 'The case for Continuous Delivery & Integration'
author: <a href="https://github.com/jkrumow/">Julian Krumow</a>
excerpt_separator: <!--end-of-excerpt-->
---
## Basic Principle

Continuous Delivery is all about automating complex actions in software production that repeat themself over and over. Simply because a machine is better at that than a human. Human beings have bad days, can be unattentive or just simply inexperienced.
<!--end-of-excerpt-->

## What automatable tasks look like

- testing of an application or parts of it
- adding new content to an application
- managing cryptographic information (keys and certificates)
- signing application code using the aforementioned information
- creating of an installation package that could be delivered to the customer

## What errors could be prevented using CI/CD

- Crashes cuased by faulty installation on customer's infrastructure / devices or on our own staging systems
- missing features and content
- invalid signatures, certificates and ids
- installation of the wrong verson of an application

## Reals World Examples

### A backend system has to delivered to the customer

Example: Volkswagen, BMW, Deutsche Bank

Automated tasks:

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
