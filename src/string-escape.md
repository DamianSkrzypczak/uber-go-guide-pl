# Używaj literałów znakowych (string literals) w celu uniknięcia znaków ucieczki (escaping)

Go wspiera [surowe literały znakowe (raw string literals)](https://go.dev/ref/spec#raw_string_lit), które mogą obejmować wiele wierszy i zawierać znaki cudzysłowu. Używaj ich, aby uniknąć ręcznego dodawania znaków ucieczki (escaping), co ułatwia lekturę.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>
