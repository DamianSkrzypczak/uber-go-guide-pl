# "nil" to poprawna wartość wycinka

`nil` to poprawna wartość wycinka o zerowej długości. Oznacza to że:

- Nie powinieneś zwracać wycinków o zerowej długości jawnie. Zamiast tego zwróć `nil`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- Aby sprawdzić czy wycinek jest pusty, zawsze stosuj wyrażenie `len(s) == 0`. Nie porównuj go z wartością `nil`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- Wartość zerowa (wycinek zadeklarowany przy użyciu `var`) nadaje się do użytku od razu, bez konieczności stosowania funkcji `make()`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // lub, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

Pamiętaj, że mimo tego że `nil` jest jak najbardziej prawidłową wartością wycinka,
nie jest tym samym co wycinek, który został zaalokowany z zerową długością - pierwszy stanowi pełnoprawną wartość `nil` a drugi nie -
przez co w niektórych sytuacjach mogą być traktowane w zupełnie inny sposób (przykładem takie sytuacji jest serializacja do formatu JSON)
