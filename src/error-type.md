# Typy błędów

Istnieje kilka opcji deklarowania błędów. Rozważ poniższe kwestie przed wyborem optymalnego rozwiązania:

- Czy kod wywołujący musi dopasować błąd, aby móc go obsłużyć?
  Jeśli tak, musimy zapewnić obsługę funkcji [`errors.Is`](https://pkg.go.dev/errors#Is) lub [`errors.As`](https://pkg.go.dev/errors#As) poprzez zadeklarowanie zmiennej błędu najwyższego poziomu lub własnego typu.
- Czy komunikat błędu jest statycznym ciągiem znaków, czy dynamicznym, wymagającym informacji kontekstowych?
  W pierwszym przypadku użyjemy [`errors.New`](https://pkg.go.dev/errors#New), w drugim – [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) lub własnego typu błędu.
- Czy propagujemy nowy błąd zwrócony przez funkcję niższego poziomu?
  Jeśli tak, zobacz [sekcję o opakowywaniu błędów](#opakowywanie-błędów-error-wrapping).

| Dopasowanie błędu? | Komunikat błędu | Wytyczne |
|-------------------|-----------------|----------|
| Nie | statyczny | [`errors.New`](https://pkg.go.dev/errors#New) |
| Nie | dynamiczny | [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) |
| Tak | statyczny | `var` najwyższego poziomu z [`errors.New`](https://pkg.go.dev/errors#New) |
| Tak | dynamiczny | własny typ `error` |

Na przykład, użyj [`errors.New`](https://pkg.go.dev/errors#New) dla błędu ze statycznym komunikatem. Wyeksportuj ten błąd jako zmienną, aby umożliwić jego dopasowanie za pomocą `errors.Is`, jeśli kod wywołujący tego wymaga.

<table>
<thead><tr><th>Brak dopasowywania błędów</th><th>Dopasowywanie błędów</th></tr></thead>
<tbody>
<tr><td>

```go
// pakiet foo

func Open() error {
  return errors.New("could not open")
}

// pakiet bar

if err := foo.Open(); err != nil {
  // Nie można obsłużyć błędu.
  panic("unknown error")
}
```

</td><td>

```go
// pakiet foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// pakiet bar

if err := foo.Open(); err != nil {
  if errors.Is(err, foo.ErrCouldNotOpen) {
    // obsługa błędu
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

W przypadku błędu z dynamicznym ciągiem znaków użyj [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf), jeśli kod wywołujący nie musi go dopasowywać, lub własnego typu `error`, gdy takie dopasowanie jest konieczne.

<table>
<thead><tr><th>Brak dopasowywania błędów</th><th>Dopasowywanie błędów</th></tr></thead>
<tbody>
<tr><td>

```go
// pakiet foo

func Open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

// pakiet bar

if err := foo.Open("testfile.txt"); err != nil {
  // Nie można obsłużyć błędu.
  panic("unknown error")
}
```

</td><td>

```go
// pakiet foo

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

func Open(file string) error {
  return &NotFoundError{File: file}
}


// pakiet bar

if err := foo.Open("testfile.txt"); err != nil {
  var notFound *NotFoundError
  if errors.As(err, &notFound) {
    // obsługa błędu
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

Pamiętaj, że eksportując zmienne lub typy błędów z pakietu, czynisz je częścią jego publicznego API.
