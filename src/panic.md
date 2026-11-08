# Nie "panikuj"

Kod działający produkcyjnie musi unikać paniki. Instrukcje `panic` są głównym źródłem tzw. [awarii kaskadowych](https://en.wikipedia.org/wiki/Cascading_failure). Jeśli błąd wystąpi, funkcja musi go zwrócić i umożliwić wywołującemu podjęcie decyzji o sposobie obsługi.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("an argument is required")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("an argument is required")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

Konstrukcja panic/recover nie stanowi strategii obsługi błędów. Program powinien panikować dopiero wtedy, gdy wystąpi sytuacja nieodzyskiwalna, taka jak dereferencja wskaźnika nil. Wyjątek stanowi etap inicjalizacji: błędy podczas uruchamiania programu, które powinny przerwać jego działanie, mogą wywołać panikę.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

Nawet w testach preferuj `t.Fatal` lub `t.FailNow` zamiast paniki, aby upewnić się, że test zostanie wyraźnie oznaczony jako nieudany.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>
