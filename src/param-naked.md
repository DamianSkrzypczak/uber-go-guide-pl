# Unikaj "surowych" wartości parametrów

Surowe wartości parametrów (naked parameters) przekazywane w wywołaniach funkcji mogą obniżać czytelność. Dodawaj komentarze w stylu języka C (`/* ... */`) z nazwami parametrów, jeśli ich znaczenie nie jest oczywiste.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

Jeszcze lepiej zastąpić typ `bool` własnym typem w celu poprawienia czytelności oraz zapewnienia bezpieczeństwa typowania. Umożliwia to również przekazanie więcej niż dwóch stanów (true/false) w przyszłości.

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status = iota + 1
  StatusDone
  // W przyszłości możemy dodać StatusInProgress.
)

func printInfo(name string, region Region, status Status)
```
