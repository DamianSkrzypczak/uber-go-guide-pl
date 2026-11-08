# Redukuj zagnieżdżenia

Kod powinien redukować zagnieżdżenia, gdzie to tylko możliwe, poprzez wcześniejszą obsługę błędów i warunków brzegowych oraz wczesny powrót z funkcji (`return`) lub kontynuację pętli (`continue`). Redukuj ilość kodu zagnieżdżonego na wielu poziomach.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>
