# Używaj tagów pól w strukturach podlegających serializacji

Każde pole struktury, które jest serializowane do formatu JSON, YAML lub innych formatów obsługujących nazewnictwo oparte na tagach, powinno być opatrzone odpowiednim tagiem.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Stock struct {
  Price int
  Name  string
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```
</td><td>

```go
type Stock struct {
  Price int    `json:"price"`
  Name  string `json:"name"`
  // Bezpieczna zmiana nazwy pola Name na Symbol.
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

Uzasadnienie:
Zserializowana postać struktury stanowi kontrakt między różnymi systemami. Zmiany w strukturze tej postaci (w tym w nazwach pól) łamią ten kontrakt. Określenie nazw pól w tagach czyni kontrakt jawnym i chroni przed przypadkowym jego naruszeniem podczas refaktoryzacji lub zmiany nazw pól.
