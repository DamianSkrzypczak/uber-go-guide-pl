# Contributing / Jak wnosić wkład

[English](#english) | [Polski](#polski)

---

<a name="english"></a>
## English

Before making any changes, please note the distinction between logical rules/discussions and translation improvements:

1. **Main Document**: The full, compiled style guide in Polish is available in [style.md](style.md).
2. **Rule Changes & Logical Discussions**: For any changes to the Go guidelines, rules, or general direction of the style guide, please visit the original repository: [uber-go/guide](https://github.com/uber-go/guide). All rule discussions and updates must be agreed upon in the main project.
3. **Translation Improvements**: If you want to fix translation errors, improve Polish terminology, or update existing texts, please contribute directly to this repository while adhering to the original structure.

### Making changes

- `style.md` is generated from the contents of the `src/` folder. All changes must be made to files in the `src/` folder.
- For new entries or modified sections, follow the structure of the original repository and update `src/SUMMARY.md` if necessary.
- Use tables for side-by-side code samples.
- Link to other sections using their `.md` file names (e.g. `[..](foo.md)`).

### Updating style.md

`style.md` is generated from the contents of `src/` using [stitchmd](https://github.com/abhinav/stitchmd).
If `style.md` is out of date, regenerate it by running:

```bash
make
```

This will install `stitchmd` if needed and update `style.md`. Commit the result and push it to your branch.

### Writing style

* **Semantic Line Breaks**: Use [semantic line breaks](https://sembr.org/) in your Markdown files for easier diffs and reviews.
* **File Names**: Files in `src/` follow the convention `{subject}-{desc}.md` (e.g., `struct-tag.md`, `var-decl.md`).
* **Code Indentation**: Use two spaces to indent code samples in Markdown tables to save horizontal space.

---

## Polski

Przed wprowadzeniem jakichkolwiek zmian zapoznaj się z poniższym podziałem dotyczącym zgłaszania uwag:

1. **Główny dokument**: Pełna, wygenerowana wersja poradnika w języku polskim znajduje się w pliku [style.md](style.md).
2. **Zmiany reguł i dyskusje logiki**: Wszelkie propozycje zmian zasad pisania kodu w Go, nowe wytyczne oraz dyskusje dotyczące ogólnego kierunku przewodnika należy zgłaszać w oryginalnym repozytorium: [uber-go/guide](https://github.com/uber-go/guide).
3. **Poprawki tłumaczenia**: Jeśli chcesz poprawić literówkę, dopracować polską terminologię lub wygładzić tłumaczenie, zgłaszaj PR bezpośrednio w tym repozytorium, zachowując przy tym strukturę plików z oryginału.

### Wprowadzanie zmian

* Plik `style.md` jest generowany automatycznie z zawartości katalogu `src/`. Wszystkie poprawki tekstowe należy wprowadzać w plikach wewnątrz folderu `src/`.
* Zachowaj tabele dla przykładów kodu „Źle / Dobrze” (*Bad / Good*).
* Linkuj do innych sekcji za pomocą oryginalnych nazw plików `.md` (np. `[..](foo.md)`).

### Aktualizacja style.md

Plik `style.md` jest budowany z plików w `src/` przy użyciu narzędzia [stitchmd](https://github.com/abhinav/stitchmd).
Aby odświeżyć `style.md` po zmianach w `src/`, uruchom:

```bash
make
```

Polecenie zainstaluje `stitchmd` (jeśli to konieczne) i zaktualizuje `style.md`. Zaznacz zmienione pliki i zmieść je w swoim PR.

### Formatowanie i styl pisania

* **Semantyczne łamanie linii**: Stosuj [semantic line breaks](https://sembr.org/) – dziel zdania na osobne linie w plikach `.md`, aby ułatwić przeglądanie zmian (diff) na GitHubie.
* **Nazwy plików**: Pliki w `src/` powinny zachowywać konwencję `{subject}-{desc}.md` zgodną z oryginalnym repozytorium.
* **Przykłady kodu**: Stosuj wcięcia z dwóch spacji w blokach kodu wewnątrz tabel, aby zaoszczędzić miejsce w poziomie.
