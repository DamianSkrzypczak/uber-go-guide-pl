# Nazewnictwo błędów

Dla wartości błędów przechowywanych jako zmienne globalne stosuj prefiks `Err` lub `err` w zależności od tego, czy są eksportowane. Ta zasada zastępuje wytyczną [Rozpoczynaj nieeksportowane zmienne globalne znakiem _](#rozpoczynaj-nieeksportowane-zmienne-globalne-znakiem-_).

```go
var (
  // Poniższe dwa błędy są eksportowane,
  // aby użytkownicy pakietu mogli je dopasować za pomocą errors.Is.

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // Ten błąd nie jest eksportowany, ponieważ
  // nie chcemy czynić go częścią publicznego API.
  // Nadal możemy go używać wewnątrz pakietu z errors.Is.

  errNotFound = errors.New("not found")
)
```

W przypadku własnych typów błędów używaj przyrostka `Error`.

```go
// Podobnie, ten błąd jest eksportowany,
// aby użytkownicy pakietu mogli go dopasować za pomocą errors.As.

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

// Ten błąd nie jest eksportowany, ponieważ
// nie chcemy czynić go częścią publicznego API.
// Nadal możemy z niego korzystać wewnątrz pakietu z errors.As.

type resolveError struct {
  Path string
}

func (e *resolveError) Error() string {
  return fmt.Sprintf("resolve %q", e.Path)
}
```
