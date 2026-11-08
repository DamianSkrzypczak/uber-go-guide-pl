# Stosuj `var` dla struktur o wartościach zerowych

Gdy wszystkie pola struktury są pomijane przy deklaracji, używaj składni ze słowem kluczowym `var`.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{}
```

</td><td>

```go
var user User
```

</td></tr>
</tbody></table>

Odróżnia to struktury o wartościach zerowych od tych zawierających niezerowe pola 
(podobnie jak przy [inicjalizacji map](map-init.md)) 
i odpowiada naszej preferencji odnośnie [deklarowania pustych wycinków].

  [deklarowania pustych wycinków](https://go.dev/wiki/CodeReviewComments#declaring-empty-slices)