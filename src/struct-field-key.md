# Jawnie podawaj nazwy pól podczas inicjalizacji struktur

Prawie zawsze powinieneś jawnie podawać nazwy pól podczas inicjalizacji struktur. Jest to obecnie wymuszane przez narzędzie [`go vet`](https://pkg.go.dev/cmd/vet).

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

Wyjątek: Nazwy pól *mogą* być pomijane w tablicach testowych składających się z 3 lub mniejszej liczby pól.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```
