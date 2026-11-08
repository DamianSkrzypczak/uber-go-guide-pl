# Unikaj używania nazw elementów wbudowanych (built-in names)

[Specyfikacja języka Go](https://go.dev/ref/spec) określa zestaw wbudowanych, 
[predefiniowanych identyfikatorów](https://go.dev/ref/spec#Predeclared_identifiers), których nie należy używać jako nazw w programach Go.

W zależności od kontekstu ponowne użycie tych identyfikatorów przesłoni (shadow) 
oryginał wewnątrz bieżącego zakresu leksykalnego (i wszystkich zakresów zagnieżdżonych) lub spowoduje, 
że kod zacznie wprowadzać w błąd. W najlepszym przypadku kompilator zgłosi błąd; 
w najgorszym kod wprowadzi ukryte, trudne do wykrycia błędy.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` przesłania typ wbudowany

// lub

func handleErrorMessage(error string) {
    // `error` przesłania typ wbudowany
}
```

</td><td>

```go
var errorMessage string
// fraza `error` nadal odwołuje się do typu wbudowanego

// lub

func handleErrorMessage(msg string) {
    // fraza `error` nadal odwołuje się do typu wbudowanego
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // Mimo że te pola technicznie nie
    // przesłaniają typu wbudowanego,
    // wyszukiwanie w tekście fraz
    // `error` lub `string` daje niejednoznaczne wyniki.
    error  error
    string string
}

func (f Foo) Error() error {
    // `error` oraz `f.error`
    // są wizualnie podobne
    return f.error
}

func (f Foo) String() string {
    // `string` oraz `f.string`
    // są wizualnie podobne
    return f.string
}
```

</td><td>

```go
type Foo struct {
    // Wyszukiwanie fraz `error` oraz `string`
    // jest teraz jednoznaczne.
    err error
    str string
}

func (f Foo) Error() error {
    return f.err
}

func (f Foo) String() string {
    return f.str
}
```

</td></tr>
</tbody></table>

Warto pamiętać, że kompilator nie wygeneruje błędów podczas korzystania z predefiniowanych identyfikatorów, 
ale narzędzia takie jak `go vet` powinny poprawnie wskazywać takie przypadki przesłaniania nazw.
