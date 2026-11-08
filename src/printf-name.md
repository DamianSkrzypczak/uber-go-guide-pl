# Nazewnictwo funkcji "Printf-style"

Kiedy deklarujesz funkcję w stylu `Printf`, upewnij się, że `go vet` jest w stanie ją wykryć i sprawdzić łańcuch formatujący.

Oznacza to, że powinieneś używać predefiniowanych nazw w stylu `Printf`, jeśli to możliwe. `go vet` domyślnie sprawdza takie funkcje. Zobacz [Printf family](https://pkg.go.dev/cmd/vet#hdr-Printf_family) po szczegóły.

Jeśli użycie predefiniowanych nazw nie jest możliwe, zakończ wybraną nazwę literą `f`: `Wrapf`, a nie `Wrap`. Narzędzie `go vet` można poprosić o sprawdzenie konkretnych funkcji, ale ich nazwa musi kończyć się literą `f`.

```shell
go vet -printfuncs=wrapf,statusf
```

Zobacz również artykuł [go vet: Printf family check](https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/).
