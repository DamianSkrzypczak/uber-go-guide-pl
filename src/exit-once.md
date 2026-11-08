# Wywołuj exit tylko raz

Jeśli to możliwe, staraj się wywoływać `os.Exit` lub `log.Fatal` **co najwyżej raz** w funkcji `main()`. Jeśli istnieje wiele scenariuszy błędów przerywających wykonanie programu, umieść tę logikę w osobnej funkcji i zwracaj z niej błędy.

Skraca to funkcję `main()` i przenosi kluczową logikę biznesową do osobnej, łatwiej testowalnej funkcji.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
package main

func main() {
  args := os.Args[1:]
  if len(args) != 1 {
    log.Fatal("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    log.Fatal(err)
  }
  defer f.Close()

  // Jeśli wywołamy log.Fatal po tej linii,
  // f.Close() nie zostanie wywołane.

  b, err := io.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  // ...
}
```

</td><td>

```go
package main

func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}

func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    return err
  }
  defer f.Close()

  b, err := io.ReadAll(f)
  if err != nil {
    return err
  }

  // ...
}
```

</td></tr>
</tbody></table>

Powyższy przykład używa `log.Fatal`, ale wytyczna dotyczy również `os.Exit` i dowolnego kodu bibliotecznego wywołującego `os.Exit`.

```go
func main() {
  if err := run(); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

Możesz dostosować sygnaturę funkcji `run()` do własnych potrzeb. Jeśli np. program musi kończyć działanie ze specyficznymi kodami wyjścia dla niepowodzeń, `run()` może zwracać kod wyjścia zamiast błędu. Umożliwia to bezpośrednią weryfikację tego zachowania w testach jednostkowych.

```go
func main() {
  os.Exit(run(args))
}

func run() (exitCode int) {
  // ...
}
```

Mówiąc ogólniej, funkcja `run()` użyta w tych przykładach nie ma charakteru nakazowego. Istnieje elastyczność w kwestii nazwy, sygnatury i konfiguracji funkcji `run()`. Możesz między innymi:

- przyjmować nieprzetworzone argumenty linii poleceń (np. `run(os.Args[1:])`)
- parsować argumenty linii poleceń w `main()` i przekazywać je do `run`
- używać własnego typu błędu do przekazania kodu wyjścia z powrotem do `main()`
- umieścić logikę biznesową w innej warstwie abstrakcji niż `package main`

Wytyczna ta wymaga jedynie, aby w `main()` istniało jedno miejsce odpowiedzialne za faktyczne zamknięcie procesu.
