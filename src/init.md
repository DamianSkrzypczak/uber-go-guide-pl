# Unikaj `init()`

Unikaj funkcji `init()` tam, gdzie to możliwe. Gdy użycie `init()` jest nieuniknione lub wysoce pożądane, kod powinien:

1. Być całkowicie deterministyczny i niezależny od środowiska czy sposobu wywołania programu.
2. Unikać zależności od kolejności lub efektów ubocznych (side-effects) innych funkcji `init()`. Podczas gdy kolejność wywoływania `init()` jest dobrze znana, kod może się zmieniać, a relacje między funkcjami `init()` mogą uczynić go podatnym na błędy.
3. Unikać dostępu lub modyfikacji stanu globalnego i środowiskowego (takich jak informacje o maszynie, zmienne środowiskowe, katalog roboczy, argumenty wejściowe programu itp.).
4. Unikać operacji wejścia/wyjścia (I/O), w tym operacji na systemie plików, sieci oraz wywołań systemowych.

Kod, który nie spełnia tych wymagań, prawdopodobnie powinien być funkcją pomocniczą wywoływaną w ramach `main()` (lub w innym momencie cyklu życia programu) bądź stanowić część samej funkcji `main()`. W szczególności biblioteki tworzone z myślą o użyciu przez inne programy powinny zachować szczególną ostrożność, aby być w pełni deterministyczne i nie wykonywać "magii podczas inicjalizacji".

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}

var _defaultFoo Foo

func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}

// lub lepiej, dla testowalności:

var _defaultFoo = defaultFoo()

func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}

var _config Config

func init() {
    // Źle: bazuje na bieżącym katalogu
    cwd, _ := os.Getwd()

    // Źle: operacje wejścia/wyjścia (I/O)
    raw, _ := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )

    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}

func loadConfig() Config {
    cwd, err := os.Getwd()
    // obsługa err

    raw, err := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // obsługa err

    var config Config
    yaml.Unmarshal(raw, &config)

    return config
}
```

</td></tr>
</tbody></table>

Biorąc pod uwagę powyższe, niektóre sytuacje, w których `init()` może być preferowane lub niezbędne, obejmują:

- Złożone wyrażenia, których nie można przedstawić jako pojedynczych przypisań.
- Wtyczki (pluggable hooks), takie jak dialekty `database/sql`, rejestry typów kodowania itp.
- Optymalizacje pod kątem [Google Cloud Functions](https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations) i innych form deterministycznych obliczeń wstępnych.
