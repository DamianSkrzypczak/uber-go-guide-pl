# Deklarowanie zmiennych lokalnych

Skrócona deklaracja zmiennych (`:=`) powinna być używana, gdy zmiennej jawnie przypisuje się wartość.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

Jednakże są przypadki, gdy wartość domyślna staje się bardziej czytelna przy użyciu słowa kluczowego `var`. Na przykład przy [deklarowaniu pustych wycinków].

  [deklarowaniu pustych wycinków]: https://go.dev/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>
