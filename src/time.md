# Używaj pakietu `"time"` do obsługi czasu

Czas to skomplikowany temat. Nieprawidłowe założenia często dotyczące czasu zakładają, że:

1. Dzień ma 24 godziny
2. Godzina składa się z 60 minut
3. Tydzień ma 7 dni
4. Rok to 365 dni
5. [Oraz wiele innych](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

Dla przykładu przypadek *1* oznacza, że dodanie 24 godzin do pewnego punktu w czasie nie zawsze zagwarantuje otrzymanie kolejnego dnia kalendarzowego.

Dlatego zawsze używaj pakietu [`"time"`](https://pkg.go.dev/time), gdy masz do czynienia z czasem, ponieważ pomaga on radzić sobie z błędnymi założeniami w bezpieczniejszy i dokładniejszy sposób.

## Używaj `time.Time` dla punktów w czasie

Użyj [`time.Time`](https://pkg.go.dev/time#Time), gdy pracujesz z punktami w czasie, oraz metod na `time.Time` przy porównywaniu, dodawaniu lub odejmowaniu czasu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

## Używaj `time.Duration` dla przedziałów czasowych

Używaj [`time.Duration`](https://pkg.go.dev/time#Duration) do obsługi przedziałów czasowych.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}

poll(10) // czy to były sekundy czy milisekundy?
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}

poll(10*time.Second)
```

</td></tr>
</tbody></table>

Wracając do przykładu z dodawaniem 24 godzin do punktu w czasie, użyta metoda zależy od intencji. Jeżeli chcemy uzyskać tę samą porę dnia, lecz następnego dnia kalendarzowego, powinniśmy użyć [`Time.AddDate`]. Jednakże, jeżeli chcemy gwarancji punktu w czasie przesuniętego dokładnie o 24 godziny, należy użyć [`Time.Add`].

  [`Time.AddDate`]: https://pkg.go.dev/time#Time.AddDate
  [`Time.Add`]: https://pkg.go.dev/time#Time.Add

```go
newDay := t.AddDate(0 /* lata */, 0 /* miesiące */, 1 /* dni */)
maybeNewDay := t.Add(24 * time.Hour)
```

## Używaj `time.Time` oraz `time.Duration` z systemami zewnętrznymi

Używaj `time.Duration` i `time.Time` w interakcjach z systemami zewnętrznymi, gdy tylko to możliwe. Na przykład:

- Flagi linii poleceń: pakiet [`flag`] wspiera `time.Duration` poprzez [`time.ParseDuration`]
- JSON: [`encoding/json`] wspiera kodowanie `time.Time` jako ciąg znaków zgodny z [RFC 3339] poprzez [metodę `UnmarshalJSON`]
- SQL: [`database/sql`] wspiera konwersję z kolumn `DATETIME` oraz `TIMESTAMP` na `time.Time` i z powrotem, jeśli sterownik to obsługuje
- YAML: [`gopkg.in/yaml.v2`] wspiera `time.Time` jako ciąg zgodny z [RFC 3339] oraz `time.Duration` poprzez [`time.ParseDuration`].

  [`flag`]: https://pkg.go.dev/flag
  [`time.ParseDuration`]: https://pkg.go.dev/time#ParseDuration
  [`encoding/json`]: https://pkg.go.dev/encoding/json
  [RFC 3339]: https://tools.ietf.org/html/rfc3339
  [`UnmarshalJSON` method]: https://pkg.go.dev/time#Time.UnmarshalJSON
  [`database/sql`]: https://pkg.go.dev/database/sql
  [`gopkg.in/yaml.v2`]: https://pkg.go.dev/gopkg.in/yaml.v2

Gdy nie jest możliwe użycie `time.Duration` w tych interakcjach, użyj `int` lub `float64` i dołącz jednostkę do nazwy pola.

Na przykład, ponieważ `encoding/json` nie obsługuje `time.Duration`, nazwa jednostki została zawarta w nazwie pola.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

Gdy nie jest możliwe użycie `time.Time` w tych interakcjach, chyba że uzgodniono inaczej, użyj typu `string` i formatuj znaczniki czasu (timestamps) zgodnie z definicją w [RFC 3339]. Format ten jest stosowany domyślnie przez [`Time.UnmarshalText`] i jest dostępny w `Time.Format` oraz `time.Parse` poprzez [`time.RFC3339`].

  [`Time.UnmarshalText`]: https://pkg.go.dev/time#Time.UnmarshalText
  [`time.RFC3339`]: https://pkg.go.dev/time#RFC3339


Choć w praktyce rzadko stanowi to problem, pamiętaj, że pakiet `"time"` nie obsługuje parsowania znaczników czasu z sekundami przestępnymi ([8728]), ani nie uwzględnia ich w obliczeniach ([15190]). Jeśli porównasz dwa punkty w czasie, różnica nie obejmie sekund przestępnych, które mogły w tym czasie wystąpić.

  [8728]: https://github.com/golang/go/issues/8728
  [15190]: https://github.com/golang/go/issues/15190