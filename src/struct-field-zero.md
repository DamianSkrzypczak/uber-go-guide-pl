# Pomijaj pola o wartościach zerowych przy inicjalizacji struktur

Podczas inicjalizowania struktur z podaniem nazw pól pomijaj pola, które przyjmują wartości zerowe, chyba że wnoszą one istotny kontekst. W przeciwnym razie pozwól Go automatycznie ustawić te pola na wartości zerowe.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

Pomaga to zredukować szum informacyjny poprzez pominięcie wartości domyślnych w danym kontekście. Określane są tylko wartości istotne.

Uwzględniaj wartości zerowe tam, gdzie nazwy pól dostarczają ważnego kontekstu. Na przykład przypadek testowy w [Tablicach testowych](#tablice-testowe) może zyskać na czytelności dzięki jawnym nazwom pól, nawet jeśli są one równe zeru.

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```
