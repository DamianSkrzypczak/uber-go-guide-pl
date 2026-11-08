# Kopiuj wycinki i mapy w punktach stycznych kodu

Ze względu na to, że wycinki i mapy zawierają wskaźniki (pointers) na przechowywane pod spodem dane, 
należy uważać na scenariusze, w których zachodzi potrzeba ich skopiowania.

## Odbieranie wycinków i map

Pamiętaj, że jeśli przechowujesz referencję do mapy lub wycinka otrzymanego jako argument, 
inny kod referujący do nich może nadal modyfikować ich zawartość.

<table>
<thead><tr><th>Źle</th> <th>Dobrze</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// Czy na pewno chciałeś zmodyfikować pole d1.trips?
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// Możemy teraz modyfikować wartość trips[0] bez wpływania na d1.trips.
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

## Zwracanie wycinków i map

Podobnie jak w poprzednim przypadku, uważaj na sytuacje, w których modyfikacja zwróconych map lub wycinków mogłaby ujawnić i naruszyć wewnętrzny stan struktury

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot zwraca aktualne statystyki.
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot nie jest już chroniony przez mutex,
// więc dowolna próba dostępu narażona jest na wyścig (data race).
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// Snapshot jest teraz kopią.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>
