# Tablice testowe

Stosuj testy oparte o tablice testowe z wykorzystaniem [podtestów (subtests)](https://go.dev/blog/subtests) w celu uniknięcia powielania kodu, gdy główna logika testów jest powtarzalna.

Jeśli testowany system musi zostać przetestowany pod kątem *wielu warunków*, w których zmieniają się określone dane wejściowe i wyjściowe, należy użyć testu opartego na tablicy testowej, aby zredukować duplikację i poprawić czytelność.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

Tablice testowe ułatwiają dodawanie kontekstu do komunikatów błędów, redukują duplikacje w logice oraz ułatwiają dodawanie nowych scenariuszy testowych.

Postępujemy zgodnie z konwencją mówiącą, że wycinek struktur testowych nazywamy `tests`, a każdy przypadek testowy `tt`. Ponadto zachęcamy do jawnego oznaczania danych wejściowych i wyjściowych prefiksami `give` oraz `want`.

```go
tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

## Unikaj niepotrzebnej złożoności w tablicach testowych

Tablice testowe mogą stać się trudne do odczytania i utrzymania, jeśli podtesty zawierają warunkowe asercje lub inną logikę rozgałęziającą. Tablic testowych **NIE należy** stosować, gdy wewnątrz podtestów wymagana jest złożona logika warunkowa (tzn. złożona logika wewnątrz pętli `for`).

Duże, złożone tablice testowe obniżają czytelność i utrzymywalność, ponieważ debugowanie niepowodzeń testów staje się utrudnione.

Tego typu tablice testowe powinny zostać podzielone na wiele osobnych tablic testowych lub osobne funkcje testowe `Test...`.

Idee, do których należy dążyć:

* Skupiaj się na jak najwęższej jednostce zachowania.
* Minimalizuj "głębokość testu" (test depth) i unikaj warunkowych asercji (patrz niżej).
* Upewnij się, że wszystkie pola tablicy są używane we wszystkich testach.
* Upewnij się, że cała logika testu uruchamia się dla wszystkich przypadków w tablicy.

W tym kontekście "głębokość testu" oznacza liczbę kolejnych asercji w danym teście, które wymagają spełnienia poprzednich asercji (podobny pomysł do złożoności cyklomatycznej). Płytsze testy oznaczają mniej zależności między asercjami i przede wszystkim mniejsze prawdopodobieństwo, że asercje będą warunkowe.

W praktyce tablice testowe stają się skomplikowane, gdy korzystają z wielu ścieżek rozgałęzień (np. `shouldError`, `expectCall`), zawierają liczne instrukcje `if` dla konkretnych oczekiwań atrap (mocków) (np. `shouldCallFoo`) lub przechowują funkcje wewnątrz tablicy (np. `setupMocks func(*FooMock)`).

Jednak przy testowaniu zachowania, które zmienia się wyłącznie na podstawie zmienionych danych wejściowych, preferowane może być pogrupowanie podobnych przypadków w tablicy testowej, aby lepiej zilustrować zmianę zachowania, zamiast rozbijania porównywalnych przypadków na osobne testy.

Jeśli ciało testu jest krótkie i proste, dopuszczalne jest posiadanie pojedynczej ścieżki rozgałęzienia dla przypadków sukcesu i błędu z polem tablicy takim jak `shouldErr` do określenia oczekiwań dotyczących błędów.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func TestComplicatedTable(t *testing.T) {
  tests := []struct {
    give          string
    want          string
    wantErr       error
    shouldCallX   bool
    shouldCallY   bool
    giveXResponse string
    giveXErr      error
    giveYResponse string
    giveYErr      error
  }{
    // ...
  }

  for _, tt := range tests {
    t.Run(tt.give, func(t *testing.T) {
      // konfiguracja mocków
      ctrl := gomock.NewController(t)
      xMock := xmock.NewMockX(ctrl)
      if tt.shouldCallX {
        xMock.EXPECT().Call().Return(
          tt.giveXResponse, tt.giveXErr,
        )
      }
      yMock := ymock.NewMockY(ctrl)
      if tt.shouldCallY {
        yMock.EXPECT().Call().Return(
          tt.giveYResponse, tt.giveYErr,
        )
      }

      got, err := DoComplexThing(tt.give, xMock, yMock)

      // weryfikacja wyników
      if tt.wantErr != nil {
        require.EqualError(t, err, tt.wantErr)
        return
      }
      require.NoError(t, err)
      assert.Equal(t, want, got)
    })
  }
}
```

</td><td>

```go
func TestShouldCallX(t *testing.T) {
  // konfiguracja mocków
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)
  xMock.EXPECT().Call().Return("XResponse", nil)

  yMock := ymock.NewMockY(ctrl)

  got, err := DoComplexThing("inputX", xMock, yMock)

  require.NoError(t, err)
  assert.Equal(t, "want", got)
}

func TestShouldCallYAndFail(t *testing.T) {
  // konfiguracja mocków
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)

  yMock := ymock.NewMockY(ctrl)
  yMock.EXPECT().Call().Return("YResponse", nil)

  _, err := DoComplexThing("inputY", xMock, yMock)
  assert.EqualError(t, err, "Y failed")
}
```
</td></tr>
</tbody></table>

Złożoność utrudnia modyfikację, zrozumienie oraz udowodnienie poprawności testu.

Choć nie ma tu sztywnych reguł, czytelność i łatwość utrzymania powinny zawsze być priorytetem przy wyborze między tablicą testową a osobnymi testami.

## Testy współbieżne (Parallel Tests)

Testy współbieżne (podobnie jak niektóre pętle uruchamiające gorutyny lub przechwytujące referencje) muszą dbać o jawne przypisywanie zmiennych pętli wewnątrz jej zasięgu, aby upewnić się, że przechowują one oczekiwane wartości.

```go
tests := []struct{
  give string
  // ...
}{
  // ...
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

W powyższym przykładzie musimy zadeklarować zmienną `tt` w zasięgu iteracji pętli z powodu użycia `t.Parallel()`. W przeciwnym razie większość lub wszystkie testy otrzymają nieoczekiwaną wartość `tt` lub wartość zmieniającą się w trakcie ich wykonywania.


