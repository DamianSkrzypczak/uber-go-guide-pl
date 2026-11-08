# Inicjalizowanie referencji do struktur

Używaj `&T{}` zamiast `new(T)` podczas inicjalizowania referencji do struktury, aby zachować spójność z inicjalizacją samych struktur.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// niespójne
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>
