# Opakowywanie błędów (Error Wrapping)

Istnieją trzy główne sposoby propagacji błędów w przypadku błędu wywołania funkcji:

- Zwróć oryginalny błąd w niezmienionej postaci.
- Dodaj kontekst za pomocą `fmt.Errorf` i czasownika `%w`.
- Dodaj kontekst za pomocą `fmt.Errorf` i czasownika `%v`.

Zwróć oryginalny błąd bez zmian, jeśli nie ma potrzeby dodawania dodatkowego kontekstu. Zachowuje to oryginalny typ i komunikat błędu. Jest to odpowiednie rozwiązanie, gdy komunikat błędu bazowego zawiera wystarczające informacje do zidentyfikowania jego źródła.

W przeciwnym razie dodawaj kontekst do komunikatu błędu tam, gdzie to możliwe, aby zamiast ogólnego "connection refused" otrzymać przydatny komunikat, np. "call service foo: connection refused".

Stosuj `fmt.Errorf` do dodawania kontekstu, wybierając między czasownikami `%w` a `%v` na podstawie tego, czy kod wywołujący powinien mieć możliwość dopasowania i wyekstrahowania przyczyny źródłowej:

- Użyj `%w`, jeśli kod wywołujący powinien mieć dostęp do błędu bazowego. Jest to dobry domyślny wybór dla większości opakowywanych błędów, jednak pamiętaj, że wywołujący mogą zacząć polegać na tym zachowaniu. Jeśli więc opakowywany błąd jest znaną zmienną (`var`) lub typem, udokumentuj go i przetestuj jako część kontraktu funkcji.
- Użyj `%v`, aby zaciemnić błąd bazowy. Kod wywołujący nie będzie mógł go dopasować, ale w przyszłości będziesz mógł zmienić czasownik na `%w`, jeśli zajdzie taka potrzeba.

Dodając kontekst do zwracanych błędów, dbaj o jego zwięzłość i unikaj zwrotów takich jak "failed to", które opisują rzeczy oczywiste i kumulują się na kolejnych poziomach stosu wywołań:

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %w", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %w", err)
}
```

</td></tr><tr><td>

```plain
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```plain
x: y: new store: the error
```

</td></tr>
</tbody></table>

Jednakże, gdy błąd ma trafić do innego systemu, wiadomość powinna jednoznacznie wskazywać na błąd (np. poprzez tag `err` lub prefiks "Failed" w logach).

Zobacz również [Don't just check errors, handle them gracefully](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully).

