# Inicjalizowanie map

Preferuj `make(..)` dla pustych map oraz map zapełnianych w czasie wykonywania kodu. Sprawia to, że inicjalizacja mapy różni się wizualnie od jej deklaracji i ułatwia późniejsze dodanie wskazówek dotyczących rozmiaru.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 jest bezpieczne dla operacji odczytu i zapisu;
  // m2 wywoła panikę przy próbie zapisu.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 jest bezpieczne dla operacji odczytu i zapisu;
  // m2 wywoła panikę przy próbie zapisu.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

Deklaracja oraz inicjalizacja są wizualnie podobne.

</td><td>

Deklaracja oraz inicjalizacja są wizualnie odróżnialne.

</td></tr>
</tbody></table>

Gdy to możliwe, podawaj wskazówki dotyczące pojemności podczas inicjalizacji funkcji `make()`. Więcej informacji znajdziesz w sekcji [Określaj wskazówki dotyczące pojemności map](#określaj-wskazówki-dotyczące-pojemności-map).

Z drugiej strony, jeśli mapa zawiera z góry ustalony zestaw elementów, użyj literału do jej inicjalizacji.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

Podstawową zasadą jest używanie literałów mapy podczas dodawania stałego zestawu elementów w czasie inicjalizacji, a w przeciwnym wypadku używanie funkcji `make` (z podaniem wskazówki rozmiaru, jeśli jest dostępna).
