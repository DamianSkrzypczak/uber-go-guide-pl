# Opcje funkcjonalne

Opcje funkcjonalne to wzorzec, w którym deklarujesz nieprzezroczysty typ `Option`, rejestrujący informacje w pewnej wewnętrznej strukturze. Przyjmujesz zmienną liczbę takich opcji i działasz na podstawie pełnej informacji zapisanej przez nie w tej wewnętrznej strukturze.

Używaj tego wzorca dla opcjonalnych argumentów w konstruktorach i innych publicznych interfejsach API, w przypadku których przewidujesz potrzebę ich późniejszego rozszerzania – zwłaszcza jeśli funkcja przyjmuje już trzy lub więcej argumentów.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// pakiet db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// pakiet db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open tworzy połączenie.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

Parametry cache i logger muszą być zawsze przekazane, nawet jeśli użytkownik chce użyć wartości domyślnych.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

Opcje są przekazywane tylko w razie potrzeby.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

Sugerowany sposób implementacji tego wzorca opiera się na interfejsie `Option` z nieeksportowaną metodą rejestrującą opcje w nieeksportowanej strukturze `options`.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open tworzy połączenie.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

Istnieje sposób implementacji tego wzorca oparty na domknięciach (closures), jednak uważamy, że powyższa wersja zapewnia autorowi większą elastyczność i jest łatwiejsza w debugowaniu i testowaniu. Umożliwia w szczególności porównywanie opcji między sobą w testach oraz atrapach (mockach), co przy domknięciach jest niemożliwe. Pozwala też opcjom implementować inne interfejsy, takie jak `fmt.Stringer`.

Zobacz również:

- [Self-referential functions and the design of options](https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html)
- [Functional options for friendly APIs](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)
