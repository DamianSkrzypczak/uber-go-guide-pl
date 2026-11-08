# Mutexy o wartości zerowej są poprawne

Wartości zerowe dla typów `sync.Mutex` oraz `sync.RWMutex` są jak najbardziej poprawne, prawie nigdy nie ma potrzeby używania wskaźnika do mutexa.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

Jeśli używasz struktury przez wskaźnik, zawarty w niej mutex powinien być jej polem niebędącym wskaźnikiem. Nie osadzaj mutexa w strukturze, nawet jeśli struktura nie jest eksportowana.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type SMap struct {
  sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

<tr><td>

Pole `Mutex` oraz metody `Lock` i `Unlock` stają się niezamierzenie częścią publicznego API struktury `SMap`.

</td><td>

Mutex oraz jego metody są szczegółami implementacji `SMap` ukrytymi przed kodem wywołującym.

</td></tr>
</tbody></table>
