# Aliasy importowanych bibliotek

Aliasy dla importowanych bibliotek muszą być stosowane w przypadku, gdy nazwa pakietu nie pasuje do ostatniego elementu ścieżki importu.

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

We wszystkich innych scenariuszach należy unikać aliasów importu, chyba że występuje bezpośredni konflikt nazw.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  runtimetrace "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>
