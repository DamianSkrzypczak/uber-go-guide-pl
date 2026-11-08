# Stosuj "defer" do operacji czyszczących (clean-up)

Używaj instrukcji `defer` do czyszczenia zasobów, takich jak pliki czy blokady (locks).

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// Łatwo o pominięcie odblokowania ze względu na wiele instrukcji "return".
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// Bardziej czytelne.
```

</td></tr>
</tbody></table>

Instrukcja `defer` ma niezwykle mały narzut i należy jej unikać jedynie wtedy, gdy można udowodnić, że czas wykonania funkcji jest rzędu nanosekund. Korzyści w czytelności kodu wynikające z jej stosowania są warte minimalnego kosztu wywołania. Powyższe stwierdzenie jest szczególnie prawdziwe dla większych metod robiących więcej niż tylko proste operacje na pamięci, w przypadku których inne operacje mają większe znaczenie niż `defer`.
