# `nameshave` Action

The `nameshave` action removes all namespaces from an XML document.

## Syntax

```xml
<nameshave in="$xml_with_namespaces" out="$no_more_namespaces"/>
```

## Usage

Stripping namespaces allows for easy access to XML elements and
attributes in a JSON [`template`](template.md).

## Example

Input:

```xml
<root xmlns="http://example.com/ns/2019/1" xmlns:side="http://example.com/ns/2019/2">
  <a>catch me </a>
  <side:a>if you can!</side:a>
</root>
```

Output:

```xml
<root>
  <a>catch me </a>
  <a>if you can!</a>
</root>
```

## Caution

Removing namespaces is prone to ambiguity. The origin of elements
carrying the same local name will be lost (see the example above).

Attributes originally in different namespaces sharing the same local name will silently override each other:

```xml
<root side:prop="1st" prop="last" xmlns:side="http://example.com/ns/2019/1"/>
```

becomes

```xml
<root prop="last"/>
```
