# Niepotrzebne instrukcje "else"

Jeśli zmienna jest ustawiana w obu odgałęzieniach instrukcji `if`, klasyczny blok `else` można zastąpić pojedynczą instrukcją `if`.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>