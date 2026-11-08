# Unikaj osadzania typów (type embedding) w strukturach publicznych

Typy osadzane w strukturach publicznych ujawniają szczegóły implementacji, ograniczają ewolucję typów oraz zaciemniają dokumentację.

Zakładając, że zaimplementowałeś różne typy list przy użyciu współdzielonego `AbstractList`, unikaj osadzania `AbstractList` w konkretnych implementacjach list. Zamiast tego jawnie zaimplementuj w swojej strukturze metody delegowujące wykonanie do abstrakcyjnego typu.

```go
type AbstractList struct {}

// Add dodaje element do listy.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove usuwa element z listy.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList stanowi listę elementów.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList stanowi listę elementów.
type ConcreteList struct {
  list *AbstractList
}

// Add dodaje element do listy.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove usuwa element z listy.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go pozwala na [osadzanie typów (type embedding)](https://go.dev/doc/effective_go#embedding) jako kompromis między dziedziczeniem a kompozycją. Typ zewnętrzny otrzymuje niejawne kopie metod typu osadzonego. Metody te domyślnie delegują zadanie do osadzonej instancji.

W procesie osadzania struktura zyskuje pole o tej samej nazwie co osadzony typ. Zatem jeśli typ osadzony jest publiczny, pole również staje się publiczne. W celu zachowania kompatybilności wstecznej każda przyszła wersja typu zewnętrznego będzie musiała zachować ten osadzony typ.

Osadzanie typów jest rzadko potrzebne. To ułatwienie pozwalające uniknąć żmudnego pisania metod delegujących.

Nawet osadzenie kompatybilnego *interfejsu* `AbstractList` zamiast struktury dałoby deweloperowi większą elastyczność na przyszłość, jednak nadal ujawniałoby informację, że konkretne listy korzystają z abstrakcyjnej implementacji.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList stanowi ogólną implementację
// dla kilku typów list elementów.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList stanowi listę elementów.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList stanowi listę elementów.
type ConcreteList struct {
  list AbstractList
}

// Add dodaje element do listy.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove usuwa element z listy.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Zarówno w przypadku osadzania struktur, jak i interfejsów, osadzany typ nakłada ograniczenia na ewolucję typu zewnętrznego.

- Dodawanie metod do osadzonego interfejsu to zmiana łamiąca kompatybilność (breaking change).
- Usuwanie metod z osadzonej struktury to zmiana łamiąca kompatybilność.
- Usuwanie typu osadzonego to zmiana łamiąca kompatybilność.
- Zastąpienie typu osadzonego (nawet zamiennikiem spełniającym ten sam interfejs) to zmiana łamiąca kompatybilność.

Choć pisanie metod delegujących bywa uciążliwe, dodatkowy wysiłek ukrywa szczegóły implementacji, pozostawia przestrzeń na zmiany i eliminuje pośrednictwo przy zapoznawaniu się z pełnym interfejsem w dokumentacji.
