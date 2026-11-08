# Kanały (channels) powinny mieć rozmiar 1 lub być niebuforowane

Zazwyczaj kanały powinny mieć rozmiar 1 lub być niebuforowane. 
Domyślnie kanały są niebuforowane i mają rozmiar 0. 
Każdy inny rozmiar musi podlegać ścisłej kontroli. 
Zastanów się co wpływa na rozmiar kanału, co zapobiegnie jego zapełnianiu się pod wpływem obciążenia 
i blokowaniu zapisu oraz co się stanie gdy taki scenariusz wystąpi.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// Na pewno wystarczy w każdym przypadku!
c := make(chan int, 64)
```

</td><td>

```go
// Rozmiar równy 1
c := make(chan int, 1) // lub
// Kanał niebuforowany, rozmiar 0
c := make(chan int)
```

</td></tr>
</tbody></table>