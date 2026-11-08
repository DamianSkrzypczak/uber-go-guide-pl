# Czekaj na zakończenie gorutyn

Dla każdej gorutyny uruchomionej przez system musi istnieć sposób na odczekanie na jej zakończenie. Istnieją dwa popularne sposoby:

- Użycie `sync.WaitGroup` do oczekiwania na wiele gorutyn. Stosuj to rozwiązanie, gdy musisz poczekać na zakończenie grupy gorutyn.

  ```go
  var wg sync.WaitGroup
  for i := 0; i < N; i++ {
    wg.Go(...)
  }

  // Oczekiwanie na zakończenie wszystkich:
  wg.Wait()
  ```

- Użycie dodatkowego kanału `chan struct{}`, który gorutyna zamyka po zakończeniu pracy. Stosuj to rozwiązanie, gdy istnieje tylko jedna gorutyna.

  ```go
  done := make(chan struct{})
  go func() {
    defer close(done)
    // ...
  }()

  // Oczekiwanie na zakończenie gorutyny:
  <-done
  ```
