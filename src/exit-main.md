# Zakończenie działania programu w `main()`

Programy w Go używają [`os.Exit`](https://pkg.go.dev/os#Exit) lub [`log.Fatal*`](https://pkg.go.dev/log#Fatal) do natychmiastowego zakończenia pracy. (Wywoływanie paniki nie jest właściwym sposobem na zamykanie programu – zobacz [Nie "panikuj"](#nie-panikuj).)

Wywołuj `os.Exit` lub `log.Fatal*` **wyłącznie w funkcji `main()`**. Wszystkie inne funkcje powinny zwracać błędy, aby zasygnalizować niepowodzenie.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}

func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }

  b, err := io.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}

func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }

  b, err := io.ReadAll(f)
  if err != nil {
    return "", err
  }

  return string(b), nil
}
```

</td></tr>
</tbody></table>

Uzasadnienie: Programy zawierające wiele funkcji kończących działanie stwarzają kilka problemów:

- Nieczytelny przepływ sterowania: Dowolna funkcja może zakończyć program, co utrudnia analizę przepływu kodu.
- Trudność w testowaniu: Funkcja przerywająca działanie programu przerwie również test, który ją wywołał. Utrudnia to testowanie i stwarza ryzyko pominięcia pozostałych testów w zestawie `go test`.
- Pominięte czyszczenie: Gdy funkcja kończy działanie programu, pomijane są wywołania kolejkowe z instrukcjami `defer`. Zwiększa to ryzyko pominięcia kluczowych zadań sprzątających.
