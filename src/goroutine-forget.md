# Unikaj uruchamiania gorutyn bez kontroli ich cyklu życia (fire-and-forget)

Gorutyny są lekkie, ale nie są darmowe: kosztują co najmniej pamięć stosu oraz czas CPU potrzebny na planowanie (scheduling). Choć koszty te są niewielkie przy typowym użyciu, mogą powodować znaczne problemy wydajnościowe w przypadku masowego uruchamiania bez kontrolowanego czasu życia. Gorutyny o niezarządzanym czasie życia mogą również uniemożliwiać odzyskiwanie nieużywanych obiektów przez Garbage Collector i przetrzymywać zasoby.

Dlatego nie dopuszczaj do wycieków gorutyn w kodzie produkcyjnym. Używaj pakietu [go.uber.org/goleak](https://pkg.go.dev/go.uber.org/goleak) do testowania wycieków gorutyn wewnątrz pakietów, które mogą je uruchamiać.

Ogólnie rzecz biorąc, każda gorutyna:

- musi mieć przewidywalny moment, w którym zakończy działanie; lub
- musi istnieć sposób na zasygnalizowanie gorutynie, że powinna się zatrzymać

W obu przypadkach kod wywołujący musi mieć możliwość zablokowania i odczekania na zakończenie gorutyny.

Na przykład:

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
go func() {
  for {
    flush()
    time.Sleep(delay)
  }
}()
```

</td><td>

```go
var (
  stop = make(chan struct{}) // nakazuje gorutynie zatrzymanie się
  done = make(chan struct{}) // informuje nas, że gorutyna zakończyła pracę
)
go func() {
  defer close(done)

  ticker := time.NewTicker(delay)
  defer ticker.Stop()
  for {
    select {
    case <-ticker.C:
      flush()
    case <-stop:
      return
    }
  }
}()

// W innym miejscu...
close(stop)  // wyślij sygnał zatrzymania
<-done       // i poczekaj na zakończenie
```

</td></tr>
<tr><td>

Brak możliwości zatrzymania tej gorutyny. Będzie działać do momentu zakończenia aplikacji.

</td><td>

Tę gorutynę można zatrzymać za pomocą `close(stop)` oraz odczekać na jej zakończenie poprzez `<-done`.

</td></tr>
</tbody></table>
