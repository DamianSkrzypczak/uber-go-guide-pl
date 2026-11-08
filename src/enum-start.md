# Rozpoczynaj wyliczenia od 1

Standardowym sposobem wprowadzania wyliczeń (enums) w Go jest zadeklarowanie własnego typu oraz zgrupowanie `const` z użyciem `iota`. Ponieważ wartością domyślną zmiennych jest 0, dobrą praktyką jest rozpoczynanie wyliczania od wartości niezerowej.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

Są przypadki, w których użycie wartości zerowej ma sens, na przykład gdy wartość zerowa opisuje pożądane zachowanie domyślne.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```


