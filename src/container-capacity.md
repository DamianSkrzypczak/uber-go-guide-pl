
# Preferuj określenie pojemności kontenerów

W miarę możliwości określaj pojemność kontenera, aby z góry przydzielić mu odpowiednią ilość pamięci. 
Operacja ta zminimalizuje kolejne alokacje (spowodowane kopiowaniem i zwiększaniem kontenera) 
w miarę dodawania nowych elementów.

## Określaj wskazówki dotyczące pojemności map

Gdy to tylko możliwe, umieszczaj wskazówki (hints) 
dotyczące pojemności podczas inicjalizacji mapy za pomocą funkcji `make()`.

```go
make(map[T1]T2, hint)
```

Zapewnienie wskazówki dotyczącej pojemności sprawia, 
że funkcja `make()` próbuje dopasować rozmiar mapy w czasie inicjalizacji, 
co redukuje czas i alokacje potrzebne na zwiększanie mapy podczas dodawania elementów.

Pamiętaj jednak, że w przeciwieństwie do wycinków (slices), 
wskazówki pojemności map nie gwarantują pełnej alokacji z góry, 
lecz służą do oszacowania liczby potrzebnych "kubełków" (hashmap buckets). 
Alokacje mogą więc nadal występować przy dodawaniu elementów, 
nawet przed osiągnięciem określonej pojemności.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
files, _ := os.ReadDir("./files")

m := make(map[string]os.DirEntry)
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := os.ReadDir("./files")

m := make(map[string]os.DirEntry, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

Mapa `m` stworzona bez wskazówki rozmiaru; mapa będzie dynamicznie zmieniać rozmiar, powodując wielokrotne alokacje.

</td><td>

Mapa `m` stworzona ze wskazówką rozmiaru; wystąpi mniej alokacji w czasie przypisywania elementów.

</td></tr>
</tbody></table>

## Określaj pojemność wycinków

Tam, gdzie to możliwe, podawaj wskazówki dotyczące pojemności podczas inicjowania wycinków za pomocą funkcji `make()`, szczególnie przy operacjach dodawania elementów (`append`).

```go
make([]T, length, capacity)
```

Inaczej niż w przypadku map, pojemność wycinków nie stanowi jedynie wskazówki: kompilator przydzieli dokładnie tyle pamięci, ile dostarczono w argumencie `capacity`, co oznacza, że kolejne operacje `append()` nie będą pociągały za sobą alokacji pamięci (do momentu, gdy długość wycinka zrówna się z jego pojemnością – po tym czasie każde kolejne dodanie elementu będzie wymagało zmiany rozmiaru w celu pomieszczenia dodatkowych elementów).


<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```plain
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```plain
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>