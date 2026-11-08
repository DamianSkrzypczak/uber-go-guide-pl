# Kolejność importowania bibliotek

Instrukcje importowania powinny być podzielone na dwie grupy:

- Biblioteka standardowa
- Cała reszta

Jest to domyślne grupowanie stosowane przez `goimports`.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

