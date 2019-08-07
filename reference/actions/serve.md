# `serve` Action

Die Serve-Action wird benutzt, um lokale Dateien an Clients auszuliefern. Die Action implementiert einen einfachen File-Server. Die Action beendet den Flow aber nicht, damit Manipulationen am gefundenen Inhalt möglich sind.

## Syntax

```xml
<flow>
  …
  <serve dir="../htdocs" fallback-doc="/index.html"/>
  …
</flow>
```

Beide Parameter sind Pflicht-Parameter:

* `dir`: Definiert das Verzeichnis mit den lokalen Dateien. Der Pfad wird relativ zum aufrufenden Flow aufgelöst. Das angegebene Verzeichnis muss existieren und sich unterhalb des Projekt-Pfades befinden.
* `fallback-doc`: Definiert ein Fallback-Dokument. Das Fallback-Dokument wird immer dann geladen, wenn eine angefragte Datei nicht existiert. Der Pfad zu dem Fallback-Dokument wird relativ zum `dir` aufgelöst.
* Die Action hat keinen Input (`in`)
* Der Output kann in Sonderfällen mit `out` umgeleitet werden

## Usage

Der Request-Pfad wird aus dem Delivery-Context `request/path` ausgelesen und relativ zum `dir` aufgelöst. Zeigt der Pfad auf ein Verzeichnis statt einer Datei, wird in dem Verzeichnis nach einer `index.html` gesucht. Endet ein Pfad auf ein Verzeichnis nicht mit einem Slash (`/`), wird ein `301 Moved Permanently` Redirect auf das selbe Verzeichnis mit einem abschließenden Slash ausgelöst.

Für alle gefundenen Dateien werden folgende HTTP-Header-Felder

* `Content-Type`
* `Content-Length`
* `Last-Modified`

und folgende Delivery-Context-Properties gesetzt

* `content/<type>` (sog. Handling-Type, z.B. `content/html` für `text/html`)
* `content/mime` (wie `Content-Type` HTTP-Header-Feld, z.B. `text/html`)

`Content-Type` und die Delivery-Context-Properties werden anhand der jeweiligen Datei-Endung bestimmt. Der Status-Code wird auf `200 OK` gesetzt.

Ist eine Datei nicht lesbar, wird der Request mit `403 Forbidden` beantwortet.

## Errors

* Parameter `dir` fehlt.
* Parameter `fallback-doc` fehlt.
* Das in `dir` angegebenes Verzeichnis existiert nicht oder ist nicht lesbar.
* Das in `dir` angegebenes Verzeichnis liegt nicht unterhalb des Projekt-Pfades.
* Das in `fallback-doc` angegebenes Dokument soll geladen werden, existiert aber nicht.
