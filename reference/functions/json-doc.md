# `json-doc()`

```
node-set json-doc(string path)
```

wandelt ein unter `path` hinterlegtes JSON Dokument in
[XML-Notation](../Templating/README.md#object-xml-notation) um, parst und
gibt das _Root-Element_ zurück. (Das entspricht dem obersten `<json>` Element.
So kann direkt auf dessen Kinder zugegriffen werden.)

`user.json`:
```json
{
  "name": "brenda"
}
```

```
json-doc("user.json")/name
```

## Beispiel: POST-Daten auslesen

Flow:
```xml
<flow>
  <echo if="json-doc('fit://request/request/body')/cool">Cool!</echo>
</flow>
```

Aufruf mit `curl`:
```
curl --data '{"cool":"dude"}' http://localhost:8080/…
```

Ausgabe:
```
Cool!
```

## Beispiel: Upstream-Antwort auswerten

Flow:
```xml
<flow>
  <request url="https://httpbin.org/anything" method="POST" content="json">
    <header name="Content-Type" value="application/json"/>
    <body src="fit://request/request/body"/>
  </request>

  <set-header name="content-type" value="text/plain"/>

  <template>
    POST data:             {{ fit-document('fit://request/request/body;filter=xml') }}
    Response:              {{ json-doc('fit://request/content/json') }}
    POST data valid?       {{ not(json-doc('fit://request/content/json')/json/@null) }}
    Response valid?        {{ boolean(json-doc('fit://request/content/json')) }}
    POST data in response: {{ json-doc('fit://request/content/json')/json }}
  </template>
</flow>
```

Aufruf mit `curl`:
```sh
curl --data '{"cool": No!}' 'http://localhost:8080/…'
```

Ausgabe:
```
    POST data:             "{\"cool\":No!}"
    POST data valid?       false
    response valid?        true
    POST data in response: null
```
