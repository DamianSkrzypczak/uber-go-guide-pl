# Zagnieżdżanie w strukturach (embedding)

Typy osadzone powinny znajdować się na samej górze listy pól struktury i być oddzielone pustą linią od pól zwykłych.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

Osadzanie powinno przynosić wymierne korzyści, takie jak dodawanie lub rozszerzanie funkcjonalności w sposób semantycznie poprawny. Powinno to odbywać się bez negatywnych skutków ubocznych dla użytkownika (zobacz też: [Unikaj osadzania typów w strukturach publicznych](#unikaj-osadzania-typów-type-embedding-w-strukturach-publicznych)).

Wyjątek: Mutexy nie powinny być osadzane, nawet w typach nieeksportowanych. Zobacz też: [Poprawność wartości zerowych Mutexów](#poprawność-wartości-zerowych-mutexów).

Zagnieżdżanie **nie powinno**:

- Być zabiegiem czysto kosmetycznym lub motywowanym jedynie wygodą.
- Utrudniać konstruowania lub użytkowania typu zewnętrznego.
- Wpływać na wartość zerową typu zewnętrznego. Jeśli typ zewnętrzny ma użyteczną wartość zerową, osadzenie typu wewnętrznego nie powinno tego zmieniać.
- Upubliczniać w efekcie ubocznym niepowiązanych funkcji lub pól.
- Eksponować typów nieeksportowanych.
- Wpływać na semantykę kopiowania typu zewnętrznego.
- Zmieniać API lub semantyki typu zewnętrznego.
- Osadzać niekanonicznej formy typu wewnętrznego.
- Upubliczniać szczegółów implementacyjnych.
- Pozwalać użytkownikowi na obserwowanie lub kontrolę elementów wewnętrznych.
- Zmieniać ogólnego zachowania funkcji wewnętrznych poprzez zawijanie (wrapping) w sposób, którego użytkownicy się nie spodziewają.

Mówiąc krótko: osadzaj świadomie i z celowością. Dobrym testem jest pytanie: "Czy wszystkie te wyeksportowane wewnętrzne metody/pola dodałbym bezpośrednio do typu zewnętrznego?". Jeśli odpowiedź brzmi "niektóre" lub "nie", nie osadzaj typu – użyj zwykłego pola.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // Źle: A.Lock() oraz A.Unlock() są teraz
    //      dostępne, nie dają korzyści
    //      i pozwalają użytkownikom kontrolować
    //      szczegóły wewnętrzne struktury A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // Dobrze: Write() jest wystawione na
    //       zewnętrznym poziomie w konkretnym celu
    //       i oddelegowuje pracę do metody Write()
    //       typu wewnętrznego.
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // Źle: wskaźnik zmienia użyteczność wartości zerowej
    io.ReadWriter

    // inne pola
}

// dalej

var b Book
b.Read(...)  // panic: nil pointer
b.String()   // panic: nil pointer
b.Write(...) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // Dobrze: posiada użyteczną wartość zerową
    bytes.Buffer

    // inne pola
}

// dalej

var b Book
b.Read(...)  // ok
b.String()   // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>
