# Unikaj tworzenia gorutyn w `init()`

Funkcje `init()` nie powinny uruchamiać gorutyn. Zobacz również [Unikaj init()](#unikaj-init).

Jeśli pakiet wymaga gorutyny w tle, musi wystawić obiekt odpowiedzialny za zarządzanie czasem życia tej gorutyny. Obiekt ten musi udostępniać metodę (`Close`, `Stop`, `Shutdown` itp.), która zasygnalizuje gorutynie w tle konieczność zatrzymania i poczeka na jej zakończenie.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func init() {
  go doWork()
}

func doWork() {
  for {
    // ...
  }
}
```

</td><td>

```go
type Worker struct{ /* ... */ }

func NewWorker(...) *Worker {
  w := &Worker{
    stop: make(chan struct{}),
    done: make(chan struct{}),
    // ...
  }
  go w.doWork()
  return w
}

func (w *Worker) doWork() {
  defer close(w.done)
  for {
    // ...
    case <-w.stop:
      return
  }
}

// Shutdown nakazuje pracownikowi zatrzymanie się
// i czeka na zakończenie zadania.
func (w *Worker) Shutdown() {
  close(w.stop)
  <-w.done
}
```

</td></tr>
<tr><td>

Uruchamia gorutynę w tle bezwarunkowo przy zaimportowaniu pakietu. Użytkownik nie ma nad nią kontroli ani możliwości jej zatrzymania.

</td><td>

Uruchamia pracownika tylko wtedy, gdy życzy sobie tego użytkownik. Udostępnia mechanizm wyłączenia, umożliwiając zwolnienie zasobów.

Pamiętaj o użyciu `WaitGroup`, jeśli pracownik zarządza wieloma gorutynami. Zobacz [Czekaj na zakończenie gorutyn](#czekaj-na-zakończenie-gorutyn).

</td></tr>
</tbody></table>
