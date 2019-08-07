# `copy` Action

The `copy` action copies data from one location to another.

## Syntax

* `in="..."` to define the input location (optional, default is `fit://request/content`)
* `out="..."` to define the output location (optional, default is `fit://request/content`)

## Usage

This action is used, when content has to be stored in a certain internal
location without being changed. It is often used to inject local data as content.

> 📎
> Make sure to store your files with proper extensions like
> `mock.json` or `data.xml` to obtain the correct MIME type.

## Example

```xml
<copy in="data.json"/>
```

## Errors

If the input is not present, a warning will be logged. But this is not
considered an error. The [Flow](../flow.md) will continue.
