# Rozpoczynaj nieeksportowane zmienne globalne znakiem _

Stosuj prefiks `_` dla nieeksportowanych `var` oraz `const` najwyższego poziomu, aby jasno poinformować czytelnika o ich globalnym zasięgu.

Uzasadnienie: Zmienne i stałe najwyższego poziomu mają zasięg pakietu. Używanie generycznych nazw ułatwia przypadkowe użycie niewłaściwej wartości w innym pliku tego samego pakietu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // Nie zobaczymy błędu kompilacji po usunięciu pierwszej linii w funkcji Bar().
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

**Wyjątek**: Nieeksportowane wartości błędów mogą używać prefiksu `err` bez znaku podkreślenia. Zobacz [Nazewnictwo błędów](#nazewnictwo-błędów).
