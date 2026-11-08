# Używaj go.uber.org/atomic

Operacje atomowe z pakietu [sync/atomic](https://pkg.go.dev/sync/atomic) operują na surowych typach danych (`int32`, `int64` itp.), przez co łatwo zapomnieć o użyciu operacji atomowej do odczytu lub modyfikacji zmiennej.

Biblioteka [go.uber.org/atomic](https://pkg.go.dev/go.uber.org/atomic) zwiększa bezpieczeństwo typów poprzez ukrycie typu bazowego. Dodatkowo zawiera wygodny typ `atomic.Bool`.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // atomic
}

func (f *foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // już działa…
     return
  }
  // uruchom Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // wyścig (race)!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // już działa…
     return
  }
  // uruchom Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>