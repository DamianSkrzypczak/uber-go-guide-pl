# Weryfikuj zgodność z interfejsem

W razie potrzeby weryfikuj zgodność z interfejsem w czasie kompilacji. Np. dla:

- Wyeksportowanych typów, które są wymagane do wdrożenia określonych interfejsów w ramach kontraktu API
- Wyeksportowanych jak i nieeksportowanych typów będących częścią grupy typów implementujących ten sam interfejs
- Innych przypadków, w których naruszenie interfejsu spowodowałoby problemy po stronie użytkowników

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}



func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}

var _ http.Handler = (*Handler)(nil)

func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

Instrukcja `var _ http.Handler = (*Handler)(nil)` spowoduje błąd kompilacji, jeżeli `*Handler` kiedykolwiek przestanie spełniać interfejs `http.Handler`.

Prawa strona przypisania powinna być wartością zerową testowanego (asserted) typu. Jest to `nil` dla wskaźników do typów (jak `*Handler`), wycinków oraz map, a także pusta struktura dla typów strukturalnych.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}

var _ http.Handler = LogHandler{}

func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```
