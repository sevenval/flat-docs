# `loop`

Input:
```json
{
  "list": [
    1,
    2,
    3
  ]
}
```

Template:
```xml
<template>
{
  "numbers": [
    {{loop list}}
      {{ . }}
    {{end}}
  ]
}
</template>
```

Output:
```json
{
  "numbers": [
    1,
    2,
    3
  ]
}
```

The `loop` command iterates over arrays. The expression given must therefore
return an array for the loop body to be executed. The loop body is invoked
once for each element in the array. For each invocation,  the context `.` will
be set to the current element of the array.

In the above example, we did not explicitly place a comma in the loop body. However,
JSON requires a comma to separate array values or pairs in objects. Therefore, the `loop`
command _automatically_ produces commas to separate its child productions.

You may also use a literal comma at the end of the loop body. This will not result in
duplicate commas. However, as a literal comma is produces as part of each iteration, the last production
will end with that comma. This may result in invalid JSON if the surrounding array or
object ends after the loop.


### Example: loop over non-JSON data

If the data to be used in a `loop` is not a JSON-Array, the `array()` function
can be used to convert the data:

Input:

```html
<html>
<body>
<ul id="list" foo="bar">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
</body>
</html>
```

Template:
```xml
<template>
[
  {{loop array(/html/body/ul/li)}}
    {{.}}
  {{end}}
]
</template>
```

Output:
```json
[
  "One",
  "Two",
  "Three"
]
```

Attributes passed to the `array()` function will be converted to elements
with the attribute's name in its `name` attribute, and the value of the
attribute as its content. This can be used to iterate over lists of attributes:

Template (identical input as above):
```xml
<template>
{
  {{loop array(//ul/@*)}}
    {{@name}}: {{.}}
  {{end}}
}
</template>
```

Output:
```json
{
  "id": "list",
  "foo": "bar"
}
```
