# Wstęp

Style to konwencje zarządzania naszym kodem. Termin "styl" jest tutaj nieco mylący, ponieważ opisane tu konwencje obejmują znacznie większy obszar niżeli tylko formatowanie plików źródłowych – tym zajmuje się za nas `gofmt`.

Celem niniejszego poradnika jest opanowanie tej złożoności poprzez szczegółowe opisanie rekomendacji i przeciwwskazań (Dos and Don'ts) stosowanych przy pisaniu kodu w Go w firmie Uber. Zasady te istnieją w celu zachowania sprawnego zarządzania bazą kodu przy jednoczesnym umożliwieniu inżynierom produktywnego korzystania z cech oraz funkcjonalności języka Go.

Poradnik został pierwotnie stworzony przez [Prashanta Varanasi] oraz [Simona Newtona] jako sposób na wprowadzenie współpracowników w język Go. Przez lata był on stale modyfikowany i dopracowywany w oparciu o informacje zwrotne od społeczności.

  [Prashant Varanasi]: https://github.com/prashantv
  [Simon Newton]: https://github.com/nomis52

Dokumentacja ta zawiera idiomatyczne konwencje dla kodu Go, przestrzegane w firmie Uber. Wiele z nich to ogólne wytyczne dla Go, podczas gdy inne stanowią rozszerzenie zewnętrznych źródeł, takich jak:

1. [Efektywny Go (Effective Go)](https://go.dev/doc/effective_go)
2. [Typowe błędy w Go (Go Common Mistakes)](https://go.dev/wiki/CommonMistakes)
3. [Uwagi z przeglądu kodu Go (Go Code Review Comments)](https://go.dev/wiki/CodeReviewComments)

Dążymy do tego, aby przykłady kodu były poprawne dla dwóch najnowszych wersji podrzędnych [wydań Go](https://go.dev/doc/devel/release).

Cały zawarty kod powinien być wolny od błędów zgłaszanych przez polecenia `golint` oraz `go vet`. Zalecamy skonfigurowanie edytora tak, aby:

- Uruchamiał `goimports` przy zapisie pliku
- Uruchamiał `golint` oraz `go vet` w celu wykrycia potencjalnych błędów

Informacje na temat edytorów i ich wsparcia dla narzędzi Go dostępne są pod linkiem:
https://go.dev/wiki/IDEsAndTextEditorPlugins