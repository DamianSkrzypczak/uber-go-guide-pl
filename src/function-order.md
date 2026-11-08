# Grupowanie i porządkowanie funkcji

- Funkcje powinny być sortowane w przybliżonej kolejności wykonywania (call order).
- Funkcje wewnątrz tego samego pliku powinny być grupowane według odbiorcy (receiver).

Dlatego najpierw w pliku powinny pojawić się funkcje eksportowane, zaraz po definicjach `struct`, `const`, `var`.

Funkcja `newXYZ()`/`NewXYZ()` może pojawić się po definicji typu, ale przed pozostałymi metodami tego odbiornika.

Z racji tego, że funkcje są grupowane według odbiorcy, ogólne funkcje narzędziowe powinny znajdować się pod koniec pliku.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>
