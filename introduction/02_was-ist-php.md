---
layout: guide

title:       "Was ist PHP?"
description: "Eine Einordnung von PHP in den Rahmen serverseitiger und clientseitiger Programmier- und Markupsprachen mit Beispielen, was mit PHP möglich ist und was nicht."
group:       "Einführung"
orderId:     2
permalink:   /jumpto/was-ist-php/
root:        ../..
creator:     hoefti

author:
    - name:    hoefti
      profile: 1

    - name:    nikosch
      profile: 2314

    - name:    hausl
      profile: 21246

    - name:    mermshaus
      profile: 15041

inhalt:
    - name:   "Was PHP ist"
      anchor: was-php-ist
      simple: "Was möglich ist"

    - name:   "Was PHP nicht ist"
      anchor: was-php-nicht-ist
      simple: "Was nicht möglich ist"

    - name:   "Merkhilfe"
      anchor: merkhilfe
      simple: ""
---

**…und was ist es nicht?**

Oft zeigt sich bei PHP-Einsteigern, dass ein grundlegendes Verständnis dafür fehlt, was PHP eigentlich genau ist, wofür es zu gebrauchen ist und eben auch, wofür es nicht zu gebrauchen ist.

<div class="alert alert-info">
    <strong>Information:</strong><br>
    Dieser Artikel beschreibt den klassischen Einsatzzweck von PHP als serverseitiger Skriptsprache. Der Fokus liegt darauf, die Rolle von PHP im Zusammenspiel von Client und Server bei der Interaktion mit Webseiten über das HTTP zu erklären. Darüber hinaus kann PHP wie viele Programmiersprachen auch zu allgemeineren Zwecken genutzt werden. Auf einem entsprechend konfigurierten System können etwa in PHP geschriebene Konsolenanwendungen oder Hintergrunddienste ausgeführt werden. Es existieren sogar Ansätze, interaktive, grafische Desktopanwendungen in PHP zu erstellen. Diese Verwendungsmöglichkeiten sind allerdings höchstens noch am Rande dem Bereich der Web-Entwicklung zuzuordnen und werden deshalb an dieser Stelle derzeit nicht näher beschrieben.
</div>

## [Was PHP ist](#was-php-ist)
{: #was-php-ist}

PHP ist eine Skriptsprache, die komplett auf dem Server verarbeitet wird. Das heißt, der Benutzer kommt mit dem Quelltext des Skripts gar nicht erst in Berührung.
PHP ist darauf ausgelegt, durch Programmoperationen eine andere Sprache, zumeist Hypertext-Markup (also HTML), zu erzeugen, die auf anderen Geräten weiterverarbeitet werden kann. Hieraus ergibt sich auch die Bedeutung des Backronyms PHP, welche PHP: Hypertext Preprocessor lautet.

Um ein PHP-Skript aufzurufen, wird ein [Request]({{ page.root }}/jumpto/request/) an den Server abgesetzt, welcher dann den PHP-Parser anweist, das entsprechende Skript zu parsen. Anschließend wird die erzeugte Ausgabe wieder zurück an den Benutzer gesendet. Lediglich diese Ausgabe des Skripts ist es dann, welche der Benutzer zu Gesicht bekommt.

**Somit ist mit PHP folgendes möglich:**

- Verarbeiten der über einen Request gesendeten Daten
- Verbindungen zu anderen Servern, Diensten etc.
- Datei- und Datenbankoperationen auf dem Server
- Aufruf oder Einbindung anderer Skripte auf dem Server


## [Was PHP nicht ist](#was-php-nicht-ist)
{: #was-php-nicht-ist}

PHP ist hingegen nicht dazu gedacht, direkt mit dem Benutzer zu interagieren, sprich: es ist nicht möglich, direkt auf Eingaben vom Benutzer zu reagieren, sofern diese nicht durch einen erneuten Request an den Server gesendet wurden.
Für direkte Interaktion mit dem Benutzer ist [JavaScript]({{ page.root }}/jumpto/javascript/) gedacht, welches (genau entgegengesetzt zu PHP) im Browser des Benutzers läuft, hingegen nicht auf dem Server ausgeführt wird.

**Somit ist mit PHP folgendes nicht möglich:**

- Direkte Verarbeitung von Benutzereingaben bzw. aktive Interaktion mit dem Anwender
- Operationen im Browser des Anwenders
- Kontrolle des Browsers, Auslesen von Browser- oder Clientsystemzuständen
- Eindeutige und unfehlbare Benutzeridentifikation


## [Merkhilfe](#merkhilfe)
{: #merkhilfe}

Im normalen Kontext – Browser ruft Website auf – stellen sich die Rollen von HTML und PHP so dar:

Der Aufruf einer HTML Seite liefert ein Hypertextdokument, das durch den Browser gerendert, d. h. angezeigt wird.
Der Aufruf einer PHP Seite startet ein Programm, das ein Hypertextdokument erzeugt. Das Hypertextdokument wird wiederum durch den Server ausgeliefert und im Browser gerendert.

![Client-Server]({{ page.root }}/images/client-server.jpg)

PHP wird also vor allen Sprachen und Formatierungen, die im Browser zum Einsatz kommen (HTML, JavaScript, CSS), ausgeführt. PHP ist von einem Server (also so gesehen von einer bestehenden Internetverbindung) abhängig, clientseitige Sprachen sind es nicht (insofern der Quelltext lokal auf dem Client-Gerät existiert). Ein HTML-Code mit Stylesheets und JavaScript-Funktionalität kann also, wenn alles auf ein Client-Gerät heruntergeladen wurde, lokal vom Browser verarbeitet werden. PHP-Code dagegen nicht.
