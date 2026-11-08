# Deklarowanie zmiennych najwyższego poziomu

Na najwyższym poziomie (poziomie pliku) używaj standardowego słowa kluczowego `var`. Nie określaj typu, chyba że jest inny niż typ przypisywanego wyrażenia.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// Ponieważ F już informuje, że zwraca string,
// nie musimy ponownie określać typu.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

Określ typ, jeśli typ wyrażenia nie odzwierciedla w pełni pożądanego typu.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F zwraca obiekt typu myError, jednak zależy nam na typie error.
```

