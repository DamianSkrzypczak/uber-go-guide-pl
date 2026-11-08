# Formatuj łańcuchy znakowe poza funkcją "Printf"

Jeśli deklarujesz formatowalne łańcuchy znakowe w stylu funkcji `Printf` poza literałem znakowym, twórz z nich stałe wartości `const`.

Pomoże to narzędziu `go vet` w przeprowadzeniu analizy statycznej ciągu formatującego.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>
