---
layout: guide

permalink: /jumpto/sql/
root: ../..
title: "SQL-Fehlerbehebung"
group: "Debugging"
orderId: 16

creator: nikosch
author:
    -   name: nikosch
        profile: 2314

    -   name: Chriz
        profile: 2081

    -   name: Manko10
        profile: 1139

    -   name: Hoefti
        profile: 1

    -   name: hausl
        profile: 21246

inhalt:
    -   name: "Lass Dir Fehler ausgeben"
        anchor: lass-dir-fehler-ausgeben
        simple: ""

    -   name: "Lass Dir die Query ausgeben"
        anchor: lass-dir-die-query-ausgeben
        simple: ""

    -   name: "Keywords"
        anchor: keywords
        simple: ""

    -   name: "Proaktive Maßnahmen"
        anchor: proaktive-massnahmen
        simple: "Mehrzeilige Queries, Backticks, Stringbegrenzer"

    -   name: "Last but not least"
        anchor: last-but-not-least
        simple: ""

---


### [Lass Dir Fehler ausgeben](#lass-dir-fehler-ausgeben)
{: #lass-dir-fehler-ausgeben}

#### [In der Entwicklungsumgebung](#in-der-entwicklungsumgebung)
{: #in-der-entwicklungsumgebung}

~~~ php
mysqli_query($dblink, $query) or die(mysqli_error($dblink));

// oder besser:

if (false === mysqli_query($dblink, $query)) {
   die(mysqli_error($dblink));
}
~~~


#### [In der Produktivumgebung](#in-der-produktivumgebung)
{: #in-der-produktivumgebung}

~~~ php
if (false === mysqli_query($dblink, $query)) {
   // Deine Fehlerbehandlung hier, z.B.
   throw new Exception(mysqli_error($dblink), mysqli_errno($dblink));
}
~~~

Natürlich kann man auch bei der Entwicklung schon vernünftig mit Exceptions arbeiten. In jedem Fall haben im Live-System `die()`-Anweisungen nichts zu suchen! Technische Fehlermeldungen sind nicht für Anwender gedacht und beinhalten ein Gefahrenpotential, weil Sie potentiellen Angreifern wertvolle Informationen liefern.

### [Lass Dir die Query ausgeben](#lass-dir-die-query-ausgeben)
{: #lass-dir-die-query-ausgeben}

PHP-generierte Queries sind meistens dynamisch (also mit variablen Parametern) und werden damit schnell unübersichtlich:

- Wo befinden sich Leerzeichen
- Wo befinden sich Stringbereiche
- Welche Zeichen sind escaped
- Welche Variableninhalte wurden eingetragen
<br> <br>

Bei der Fehlersuche sollte die erste Maßnahme sein, sich die „gerenderte“ Query ausgeben zu lassen, also den Querystring, der auch an die Datenbank via `mysqli_query()` übergeben wird.


### [Keywords](#keywords)
{: #keywords}

Keywords sind heiße Kandidaten für unbestimmbare Fehler. Konsultiere das Datenbank-Manual und prüfe die Liste der reservierten Wörter.

Datenbanken benutzen Keywords in Ihrer Syntax, die dann nicht gleichzeitig Feldnamen sein sollten. Das kann zu schwer bestimmbaren Syntaxfehlern führen. So sind bspw. „Alter“, „Show“ oder „Limit“ klassische Feldnamen, die zu Fehlern führen. Merke: Am besten durchweg Bezeichner in Backticks einschließen.

### [Proaktive Maßnahmen](#proaktive-massnahmen)
{: #proaktive-massnahmen}

#### [Schreibe mehrzeilig](#schreibe-mehrzeilig)
{: #schreibe-mehrzeilig}

Negativbeispiel - unübersichtliches Statement

~~~ php
$query = 'SELECT Name , Age FROM Users WHERE Name LIKE "%Horst%" OR `Register` < "2008-01-01"';
~~~

Mehrzeilige Statements

- sind besser zu lesen und damit leichter zu debuggen
- ergeben in Fehlermeldungen genauere Positionsmeldungen (`„near ... at line 2“`)
<br> <br>

Aber richtig!

Mehrzeilig heißt nicht, jede Zeile in PHP aus eigenenständigen Strings zu konkatenieren:

Negativbeispiel - mehrzeilig aber syntaktisch falsch

~~~ php
$query = 'SELECT Name , Age ' .
         'FROM   Users' . // hier entsteht ein Fehler (fehlendes Leerzeichen)
         'WHERE  Name LIKE "%Horst%"' .
         'OR `Register` < "2008-01-01"';
~~~

Dieses Verfahren ist unnötig und auch fehleranfällig. An der oben kommentierten Stelle entsteht in der Query später ein „UsersWHERE“, ohne dass das im Quellcode offenkundig ist.

MySQL stört sich nicht an Zeilenumbrüchen und auch PHP kann damit problemlos umgehen.

Positivbeispiel - übersichtliches Statement

~~~ php
$query = 'SELECT Name ,
                 Age
          FROM   Users
          WHERE  Name LIKE "%Horst%"
                 OR `Register` < "2008-01-01"';
~~~

#### [Benutze Backticks (MySQL)](#benutze-backticks-mysql)
{: #benutze-backticks-mysql}

In Backticks eingeschlossene Begriffe kollidieren nicht mit den Syntaxbestandteilen der Sprache. Damit können Keyword-Probleme umgangen werden.<br>
**Achtung!** Backticks ist MySQL-spezifisch. Wird ein anderes DBMS als MySQL benutzt (z.b. MS-SQL, PostgreSQL, ...), so sind sehr warscheinlich andere Zeichen zu verwenden, um die selbe Wirkung zu erzielen. Ein Blick in die jeweilige Doku hilft hier weiter.


##### [Benutze richtige Backticks](#benutze-richtige-backticks)
{: #benutze-richtige-backticks}

Backticks sind keine String-Begrenzer!

Begrenzer  Bedeutung

~~~
'  String
"  String
`  Backtick, schließt Bezeichner ein
~~~

Strings gehören nicht in Backticks, Feldnamen nicht in Stringbegrenzer. Auch wenn MySQL hier manchmal etwas weniger strikt ist, bitte gar nicht erst angewöhnen!


##### [Benutze Backticks richtig](#benutze-backticks-richtig)
{: #benutze-backticks-richtig}

Richtig:

~~~ php
`Feldname`
`Tabellenname`.`Feldname`
`Datenbankname`.`Tabellenname`.`Feldname`
~~~

Falsch:

~~~ php
`Feldname `
`Tabellenname.Feldname`
~~~


#### [Benutze Stringbegrenzer konsequent](#benutze-stringbegrenzer-konsequent)
{: #benutze-stringbegrenzer-konsequent}

Wie eben geschrieben erlaubt SQL zwei verschiedene Stringbegrenzer - einfache und doppelte Hochkommata. Da das gleiche für PHP gilt, ist es eine gute Idee, konsequent einen Stringbegrenzer für PHP und einen für SQL zu benuzten, um Escaping vermeiden zu können. Diese Überlegungen haben nichts mit Feldinhalten zu tun (und dort evtl. auftretenden Hochkommata); hierfür ist allein `mysqli_real_escape_string()` verantwortlich!

Negativbeispiele - kollidierende Stringbegrenzer benötigen Escaping

~~~ php
$id = (int)$id;
$query = "SELECT `Name`  ,
                 `Age`   ,
                 \"Mitarbeiter\" AS Type
          FROM    Users
          WHERE   ID = $id";


$query = 'SELECT `Name`  ,
                 `Age`   ,
                  IF(Sex=\'m\' , \'Mitarbeiter\' , \'Mitarbeiterin\')  AS Type
          FROM    Users';
~~~


Auch den Wechsel der für PHP und SQL zuständigen Begrenzer sollte man vermeiden (vgl. auch Ausführungen zu mehrzeiligen Statements).

Negativbeispiel - Wechsel der Stringbegrenzer ist unübersichtlich

~~~ php
$name = mysqli_real_escape_string($dblink, $name);
$query = 'SELECT `Name` ,
                 `Age`
          FROM    Users
          WHERE  `Name` LIKE "%' . $name . '%" ' .
                 "OR `Register` < '2008-01-01'";

~~~


Positivbeispiele - je ein Stringbegrenzer pro „Sprache“

~~~ php
$id = (int)$id;
$query = "SELECT `Name`  ,
                 `Age`   ,
                  'Mitarbeiter' AS Type
          FROM    Users
          WHERE   ID = $id";
// bzw.
$query = 'SELECT `Name`  ,
                 `Age`   ,
                  "Mitarbeiter" AS Type
          FROM    Users
          WHERE   ID = ' . (int)$id;


$query = 'SELECT `Name`  ,
                 `Age`   ,
                  IF(Sex="m" , "Mitarbeiter" , "Mitarbeiterin")  AS Type
          FROM    Users';


$name = mysqli_real_escape_string($dblink, $name);
$query = 'SELECT `Name` ,
                 `Age`
          FROM    Users
          WHERE  `Name` LIKE "%' . $name . '%"
                  OR `Register` < "2008-01-01"';
~~~

In den jeweils ersten Beispielen wird eine Variable im String verwendet (Variablenparsing), was als Stringbegrenzer für PHP die doppelten Hochkommata erfordert. Im Positivbesipiel ist die selbe Query in Alternativschreibweise mit einfachen Hochkommata ergänzt.


### [Last but not least](#last-but-not-least)
{: #last-but-not-least}

Wenn Du trotzdem im Forum posten musst, gib uns alle Informationen, die Dir die obigen Schritte geliefert haben: sauber formatierte Query, SQL-Fehlermeldung.
