# Obsługa błędów asercji typów

Forma z pojedynczą wartością zwracaną z [asercji typu] wywoła panikę przy przekazaniu nieprawidłowego typu. Dlatego zawsze stosuj konwencję "comma ok".

  [asercji typu]: https://go.dev/ref/spec#Type_assertions

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // obsłuż błąd w bezpieczny sposób (gracefully)
}
```

</td></tr>
</tbody></table>
