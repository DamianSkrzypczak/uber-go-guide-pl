# Obsługuj błędy tylko raz

Gdy kod wywołujący otrzyma błąd od wywoływanego, może go obsłużyć na różne sposoby w zależności od posiadanych informacji.

Opcje te obejmują między innymi:

- dopasowanie błędu za pomocą `errors.Is` lub `errors.As` i odpowiednią obsługę odgałęzień (jeśli kontrakt wywoływanego definiuje konkretne błędy)
- zalogowanie błędu i łagodne przejście w stan obniżonej wydajności/funkcjonalności (degrade gracefully), jeśli błąd jest odzyskiwalny
- zwrócenie dobrze zdefiniowanego błędu domenowego, jeśli problem dotyczy specyficznego stanu domeny
- zwrócenie błędu w formie [opakowanej](#opakowywanie-błędów-error-wrapping) lub dosłownej

Niezależnie od sposobu obsługi, błąd powinien być zazwyczaj obsługiwany **tylko raz**. Kod wywołujący nie powinien np. logować błędu, a następnie go zwracać, ponieważ jego własny kod wywołujący również może ten błąd obsłużyć.

Rozważ poniższe przypadki:

<table>
<thead><tr><th>Opis</th><th>Kod</th></tr></thead>
<tbody>
<tr><td>

**Źle**: Zaloguj błąd i go zwróć

Kod wywołujący w dalszej części stosu z dużym prawdopodobieństwem podejmie podobne działanie. Generuje to duży hałas w logach przy znikomej wartości.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  // ŹLE: Zobacz opis
  log.Printf("Could not get user %q: %v", id, err)
  return err
}
```

</td></tr>
<tr><td>

**Dobrze**: Opakuj błąd i go zwróć

Wyższe warstwy stosu wywołań obsłużą błąd. Użycie `%w` daje pewność, że będą mogły dopasować błąd za pomocą `errors.Is` lub `errors.As`.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  return fmt.Errorf("get user %q: %w", id, err)
}
```

</td></tr>
<tr><td>

**Dobrze**: Zaloguj błąd i przejdź w stan obniżonej funkcjonalności

Jeśli operacja nie jest bezwzględnie konieczna, możemy zapewnić działanie aplikacji w trybie obniżonej funkcjonalności poprzez podjęcie próby naprawczej.

</td><td>

```go
if err := emitMetrics(); err != nil {
  // Niepowodzenie zapisu metryk nie powinno
  // przerywać działania aplikacji.
  log.Printf("Could not emit metrics: %v", err)
}

```

</td></tr>
<tr><td>

**Dobrze**: Dopasuj błąd i przejdź w stan obniżonej funkcjonalności

Jeśli wywoływany kod definiuje konkretny błąd w swoim kontrakcie, a błąd jest odzyskiwalny, dopasuj ten przypadek i obsłuż go łagodnie. Dla pozostałych przypadków opakuj błąd i zwróć go.

Wyższe warstwy stosu obsłużą pozostałe błędy.

</td><td>

```go
tz, err := getUserTimeZone(id)
if err != nil {
  if errors.Is(err, ErrUserNotFound) {
    // Użytkownik nie istnieje. Użyj UTC.
    tz = time.UTC
  } else {
    return fmt.Errorf("get user %q: %w", id, err)
  }
}
```

</td></tr>
</tbody></table>
