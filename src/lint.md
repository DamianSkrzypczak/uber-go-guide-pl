# Lintowanie

Ważniejsza od jakichkolwiek "błogosławionych" zestawów linterów jest konsekwencja w lintowaniu całej bazy kodu.

Zalecamy stosowanie przynajmniej wymienionych linterów, ponieważ uważamy, że pomogą one wyłapać najczęstsze błędy oraz ustanowią wysoki próg jakościowy bez zbędnego narzucania reguł:

- [errcheck](https://github.com/kisielk/errcheck) do upewnienia się, że błędy są obsługiwane
- [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports) do formatowania kodu i zarządzania importami
- [revive](https://github.com/mgechev/revive) do wskazywania typowych błędów stylistycznych
- [govet](https://pkg.go.dev/cmd/vet) do analizy kodu pod kątem typowych błędów
- [staticcheck](https://staticcheck.dev) do różnorodnych analiz statycznych

  > **Uwaga**: [revive](https://github.com/mgechev/revive) jest nowoczesnym, szybszym następcą przestarzałego narzędzia [golint](https://github.com/golang/lint).

## Runnery linterów

Zalecamy stosowanie [golangci-lint](https://github.com/golangci/golangci-lint) jako głównego runnera do lintowania kodu w języku Go, przede wszystkim z powodu jego wydajności w dużych bazach kodu oraz możliwości konfiguracji i używania wielu standardowych linterów jednocześnie. Repozytorium to posiada przykład pliku konfiguracyjnego [.golangci.yml](https://github.com/uber-go/guide/blob/master/.golangci.yml) zawierającego rekomendowane lintery oraz ustawienia.

golangci-lint udostępnia [wiele linterów](https://golangci-lint.run/usage/linters/). Wyżej wymienione lintery są rekomendowane jako zestaw podstawowy, jednak zachęcamy zespoły do dodawania dodatkowych linterów mających sens w kontekście ich projektów.