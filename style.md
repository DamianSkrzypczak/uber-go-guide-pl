<!--

Editing this document:

- Discuss all changes in GitHub issues first.
- Update the table of contents as new sections are added or removed.
- Use tables for side-by-side code samples. See below.

Code Samples:

Use 2 spaces to indent. Horizontal real estate is important in side-by-side
samples.

For side-by-side code samples, use the following snippet.

~~~
<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
BAD CODE GOES HERE
```

</td><td>

```go
GOOD CODE GOES HERE
```

</td></tr>
</tbody></table>
~~~

(You need the empty lines between the <td> and code samples for it to be
treated as Markdown.)

If you need to add labels or descriptions below the code samples, add another
row before the </tbody></table> line.

~~~
<tr>
<td>DESCRIBE BAD CODE</td>
<td>DESCRIBE GOOD CODE</td>
</tr>
~~~

-->

# Uber Go Style Guide

## Spis treści

- [Wstęp](#wstęp)
- [Wskazówki](#wskazówki)
  - [Wskaźniki typów interfejsowych](#wskaźniki-typów-interfejsowych)
  - [Weryfikuj zgodność z interfejsem](#weryfikuj-zgodność-z-interfejsem)
  - [Odbiorniki (Receivers) i Interfejsy](#odbiorniki-receivers-i-interfejsy)
  - [Poprawność wartości zerowych Mutexów](#poprawność-wartości-zerowych-mutexów)
  - [Ograniczenia kopiowania wycinków i map](#ograniczenia-kopiowania-wycinków-i-map)
  - [Stosuj "defer" by opóźnić operacje czyszczenia (clean-up)](#stosuj-defer-by-opóźnić-operacje-czyszczenia-clean-up)
  - [Kanały (channels) powinny mieć rozmiar 1 lub być niebuforowane](#kanały-channels-powinny-mieć-rozmiar-1-lub-być-niebuforowane)
  - [Wyliczanie rozpoczynaj od 1](#wyliczanie-rozpoczynaj-od-1)
  - [Używaj pakietu `"time"` do obsługi czasu](#używaj-pakietu-time-do-obsługi-czasu)
  - [Typy błędów](#typy-błędów)
  - [Opakowywanie błędów (Error wrapping)](#opakowywanie-błędów-error-wrapping)
  - [Obsługa błędów asercji typów](#obsługa-błędów-asercji-typów)
  - [Nie "panikuj"](#nie-panikuj)
  - [Używaj go.uber.org/atomic](#używaj-gouberorgatomic)
  - [Unikaj mutowalnych zmiennych globalnych](#unikaj-mutowalnych-zmiennych-globalnych)
  - [Unikaj osadzania typów (type embedding) w strukturach publicznych](#unikaj-osadzania-typów-type-embedding-w-strukturach-publicznych)
- [Wydajność](#wydajność)
  - [Preferuj strconv ponad fmt](#preferuj-strconv-ponad-fmt)
  - [Unikaj konwersji string-to-byte](#unikaj-konwersji-string-to-byte)
  - [Określaj wskazówki dotyczące pojemności map](#określaj-wskazówki-dotyczące-pojemności-map)
- [Styl](#styl)
  - [Spójność ponad wszystko](#spójność-ponad-wszystko)
  - [Grupuj podobne deklaracje](#grupuj-podobne-deklaracje)
  - [Kolejność importowania bibliotek](#kolejność-importowania-bibliotek)
  - [Nazwy pakietów](#nazwy-pakietów)
  - [Nazwy funkcji](#nazwy-funkcji)
  - [Aliasy importowanych bibliotek](#aliasy-importowanych-bibliotek)
  - [Grupowanie i porządkowanie funkcji](#grupowanie-i-porządkowanie-funkcji)
  - [Redukuj zagnieżdzenia](#redukuj-zagnieżdzenia)
  - [Niepotrzebne instrukcje "else"](#niepotrzebne-instrukcje-else)
  - [Deklarowanie zmiennych najwyższego poziomu](#deklarowanie-zmiennych-najwyższego-poziomu)
  - [Rozpoczynaj nieeksportowane zmienne globalne znakiem _](#rozpoczynaj-nieeksportowane-zmienne-globalne-znakiem-_)
  - [Zagnieżdzanie w strukturach (embedding)](#zagnieżdzanie-w-strukturach-embedding)
  - [Jawnie podawaj nazwy pól podczas inicjalizacji struktur](#jawnie-podawaj-nazwy-pól-podczas-inicjalizacji-struktur)
  - [Deklarowanie zmiennych lokalnych](#deklarowanie-zmiennych-lokalnych)
  - ["nil" to poprawna wartość wycinka](#nil-to-poprawna-wartość-wycinka)
  - [Redukuj zasięg (scope) zmiennych](#redukuj-zasięg-scope-zmiennych)
  - [Unikaj przekazywania "surowych" wartości parametrów](#unikaj-przekazywania-surowych-wartości-parametrów)
  - [Używaj literałów znakowych (string literals) w celu uniknięcia znaków ucieczki](#używaj-literałów-znakowych-string-literals-w-celu-uniknięcia-znaków-ucieczki)
  - [Inicjalizowanie referencji do struktur](#inicjalizowanie-referencji-do-struktur)
  - [Inicjalizowanie map](#inicjalizowanie-map)  
  - [Formatuj łańcuchy znakowe poza funkcją "Printf"](#formatuj-łańcuchy-znakowe-poza-funkcją-Printf)
  - [Nazewnictwo funkcji "Printf-style"](#nazewnictwo-funkcji-printf-style)
- [Wzorce](#wzorce)
  - [Tablice testowe](#tablice-testowe)
  - [Opcje funkcjonalne](#opcje-funkcjonalne)

## Wstęp

Style to konwencje zarządzania naszym kodem.
Termin "styl" jest tutaj nieco mylący, ponieważ opisane tu konwencje obejmują znacznie większy obszar niżeli tylko formatowanie plików źródłowych, które zresztą robi za nas `gofmt`.  

Celem niniejszego poradnika jest ustrukturyzowanie tych kowencji poprzez szczegółowe opisanie rekomendacji i przeciwwskazań (Dos and Don'ts) stosowanych przy pisaniu kodu w Go w firmie Uber. Zasady te istnieją w celu zachowania sprawnego zarządzania bazą kodu przy jednoczesnym umożliwieniu inżynierom produktywnego korzystania z cech oraz funkcjonalności języka Go.

Poradnik został pierwotnie napisany przez [Prashanta Varanasi] oraz [Simona Newtona] jako sposób na wprowadzenie współpracowników w język Go. Przez lata był on stale modyfikowany i dopracowywany bazując na otrzymywanych informacjach zwrotnych.

  [Prashanta Varanasi]: https://github.com/prashantv
  [Simona Newtona]: https://github.com/nomis52

Dokumentacja ta zawiera idiomatyczne konwencje dla kodu Go, przestrzegane w firmie Uber. Wiele z nich to oficjalne wytyczne dla Go podczas, gdy inne pochodzą z zewnętrznych źródeł takich jak:

1. [Efektywny Go (Effective Go)](https://golang.org/doc/effective_go.html)
2. [Przewodnik po typowych błędach Go (The Go common mistakes guide)](https://github.com/golang/go/wiki/CodeReviewComments)

Cały zawarty kod powinien być wolny od błędów zgłaszanych przez polecenia `golint` oraz `go vet`. Zalecamy skonfigurowanie edytora tak by:

- Uruchamiać `goimports` po każdym zapisie pliku
- Uruchamiać `golint` oraz `go vet` w celu wykrycia potencjalnych błędów

Informacje na temat edytorów i ich wsparcia dla języka Go dostępne są pod linkiem:
<https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>

## Wskazówki

### Wskaźniki typów interfejsowych

Praktycznie nigdy nie ma potrzeby używania wskaźników do interfejsów.
Powinieneś przekazywać interfejs poprzez wartość, ponieważ w dalszym ciągu możliwe jest aby przekazywany obiekt był wskaźnikiem na dane.  

Interfejs składa się z dwóch pól:

1. Wskaźnik na pewne informacje specyficzne dla typu. Możesz myśleć o tym jako o "typie".
2. Wskaźnik na dane. Jeśli wartością jest wskaźnik, jest on przechowywany bezpośrednio. W innym przypadku, gdy wartością są dane, przechowywany zostaje  wskaźnik na te dane.

Jeśli więc chcesz, aby metody typu interfejsowego modyfikowały jego dane, musisz w nich używać wskaźnika.

### Weryfikuj zgodność z interfejsem

W razie potrzeby weryfikuj zgodność z interfejsem w czasie kompilacji.
Np. dla:

- Wyeksportowanych typów, które są wymagane do wdrożenia określonych interfejsów w ramach kontraktu API
- Wyexportowanych jak i nieexportowanych typów będących częscią grupy typów implementujących ten sam interfejs
- Innych przypadków, w których naruszenie interfejsu spowodowałoby problemy po stronie użytkowników

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}
func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}
var _ http.Handler = (*Handler)(nil)
func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

Instrukcja `var _ http.Handler = (*Handler)(nil)` spowoduje błąd kompilacji jeżeli `*Handler` kiedykolwiek przestanie spełniać interfejs `http.Handler`.

Prawa strona przypisania powinna być wartością zerową testowanego (asserted) typu. Jest to `nil` dla wskaźników do typów (jak `*Handler`), wycinków oraz map oraz pusta instancja dla struktur.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}
var _ http.Handler = LogHandler{}
func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

### Odbiorniki (Receivers) i Interfejsy

Metody przynależące do odbiornika mogą być wywoływane zarówno na jego wskaźnikach jak i wartościach.

Na przykład:

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

sVals := map[int]S{1: {"A"}}

// Możesz wywoływać "Read" jedynie poprzez wartość
sVals[1].Read()

// Kompilacja tego kodu się nie powiedzie:
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// Możesz wywoływać zarówno "Read" jak i "Write" jeśli użyjesz wskaźnika  
sPtrs[1].Read()
sPtrs[1].Write("test")
```

Podobnie, interfejs może zostać spełniony poprzez wskaźnik, nawet jeśli odbiornikiem zadeklarowanej metody jest wartość, nie wskaźnik.

```go
type F interface {
  f()
}
Typy błędów
func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// Kompilacja poniższego kodu się nie powiedzie, ponieważ s2Val przechowyje wartość, a odbiornikiem dla f jest wskaźnik.
//   i = s2Val
```

Efektywny Go dobrze opisuje ten przypadek w rozdziale [Pointers vs. Values].

  [Pointers vs. Values]: https://golang.org/doc/effective_go.html#pointers_vs_values

### Poprawność wartości zerowych Mutexów

Wartości zerowe dla typów `sync.Mutex` oraz `sync.RWMutex` są jak najbardziej poprawne, prawie nigdy nie ma potrzeby na używanie wskaźników na mutex.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

Jeśli używasz struktury przez wskaźnik, zawarty w niej mutex nie musi być wskaźnikiem.

Struktury nieeksportowane wykorzystujące mutex do ochrony pól, mogą go zawierać (osadzać - embed).

<table>
<tbody>
<tr><td>

```go
type smap struct {
  sync.Mutex // tylko dla nieeksportowanych typów

  data map[string]string
}

func newSMap() *smap {
  return &smap{
    data: make(map[string]string),
  }
}

func (m *smap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

</tr>
<tr>
<td>Osadź w prywatnych typach lub typach, które muszą implementować interfejs Mutex.</td>
<td>Dla eksportowanych typów, użyj pól prywatnych.</td>
</tr>

</tbody>
</table>

### Ograniczenia kopiowania wycinków i map

Ze względu na to że wycinki i mapy zawierają wskaźniki na przechowywane dane, należy uważać na scenariusze kiedy trzeba je skopiować.

#### Odbieranie wycinków i map

Pamiętaj, że użytkownicy mogą modyfikować zawartość map lub wycinków które uzyskałeś jako argument, gdy przechowywujesz do nich referencje.

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

// Czy chciałeś zmodyfikować pole d1.trips?
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

#### Zwracanie wycinków i map

Podobnie do powyższego, bądź świadom potencjalnych modyfikacji map lub wycinków udostępniających stan wewnętrzny.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot zwraca aktualny status
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot nie jest już dłużej chroniony przez mutex,
// więc dowolna próba dostępu może stać się częścią wyścigu (data race).
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

// Snapshot to teraz kopia.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

### Stosuj "defer" by opóźnić operacje czyszczenia (clean-up)

Użyj instrukcji "defer" do czyszczenia zasobów takich jak pliki czy blokady (locks).

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// łatwo o pominięcie odblokowywania
/// ze wzgledu na wiele wystąpień instrukcji "return"
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// bardziej czytelny
```

</td></tr>
</tbody></table>

Defer ma niezwykle mały narzut i należy go unikać jedynie wtedy, gdy można udowodnić, że czas wykonania funkcji jest rzędu nanosekund.
Korzyści w czytelności kodu wynikające z instrukcji defer są warte minimalnego kosztu za jej wykorzystanie.
Powyższe stwierdzenie jest szczególnie prawdziwe dla większych metod operujących nie tylko na pamięci, w przypadku których koszt innych operacji znacząco przewyższa koszt instrukcji `defer`.

### Kanały (channels) powinny mieć rozmiar 1 lub być niebuforowane

Domyślnie kanały są niebuforowane a ich rozmiar wynosi 0. Każdy inny rozmiar powinien podlegać ścisłej kontroli.
Zastanów się, co wpływa na rozmiar kanału, co zapobiegnie jego zapełnianiu się pod wpływem obciążenia oraz blokowaniu zapisu i co jeśli taki scenariusz się wydarzy.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// Naaaapewno dla wszystkich wystarczy!
c := make(chan int, 64)
```

</td><td>

```go
// Rozmiar równy 1
c := make(chan int, 1) // lub
// Niebuforowany, o rozmiarze 0
c := make(chan int)
```

</td></tr>
</tbody></table>

### Wyliczanie rozpoczynaj od 1

Standardowym sposobem wprowadzenia wyliczeń (enums) w Go jest zadeklarowanie  własnego typu oraz zgrupowanie `const` z użyciem `iota`.
Ponieważ wartością domyślną zmiennych jest 0, dobrą praktyką jest rozpoczynianie wyliczania od wartości niezerowej.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

Są oczywiście przypadki gdzie rozpoczynanie od zera ma sens, na przykład gdy wartość zerowa opisuje zachowanie domyślne.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

### Używaj pakietu `"time"` do obsługi czasu

Czas to skomplikowany temat. Nieprawidłowe założenia często dotyczące „czasu” zakładają że:

1. Dzień ma 24 godziny
2. Godzina składa się z 60 minut
3. Tydzień ma 7 dni
4. Rok to 365 dni
5. [Oraz wiele wiele innych](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

Dla przykładu przypadek *1* pokazuje, że dodanie 24 godzin do pewnego punktu w czasie nie zagwarantuje otrzymania kolejnego dnia kalendarzowego.

#### Używaj `time.Time` dla punktów w czasie

Użyj [`time.Time`] gdy masz do czynienia z punktami w czasie. Metody `time.Time` wykorzystuj do porównywania, dodawania lub odejmowania czasu.

  [`time.Time`]: https://golang.org/pkg/time/#Time

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

#### Używaj `time.Duration` dla przedziałów czasowych

Use [`time.Duration`] when dealing with periods of time.

  [`time.Duration`]: https://golang.org/pkg/time/#Duration

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}
poll(10) // czy to były sekundy czy milisekundy?
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}
poll(10*time.Second)
```

</td></tr>
</tbody></table>

Wracając do przykładu z dodawaniem 24 godzin to punktu w czasie, metoda której użyjemy do dodawania zależy od intencji. Jeżeli chcemy uzyskać tą samą porę, lecz następnego dnia, powinniśmy użyć [`Time.AddDate`]. Jednakże, jeżeli chcemy gwarancji że uzyskamy punkt w czasie przesunięty o 24 od podanego powinniśmy posłużyć się [`Time.Add`].

  [`Time.AddDate`]: https://golang.org/pkg/time/#Time.AddDate
  [`Time.Add`]: https://golang.org/pkg/time/#Time.Add

```go
newDay := t.AddDate(0 /* lata */, 0, /* miesiące */, 1 /* dni */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### Używaj `time.Time` oraz `time.Duration` z systemami zewnętrznymi

Używaj `time.Time` i `time.Duration` w interakcjach z systemami zewnętrznymi gdy tylko to możliwe.

Na przykład:

- Flagi lini poleceń: pakiet [`flag`] wspiera `time.Duration` poprzez
  [`time.ParseDuration`]
- JSON: [`encoding/json`] wspiera kodowanie `time.Time` jako [RFC 3339] string poprzez [metodę `UnmarshalJSON`]
- SQL:  [`database/sql`] wspiera konwersję z kolumn `DATETIME` oraz `TIMESTAMP` w `time.Time` i spowrotem jeśli sterownik to obsługuje.
- YAML: [`gopkg.in/yaml.v2`] wspiera `time.Time` jako [RFC 3339] string, oraz `time.Duration` poprzez [`time.ParseDuration`].

  [`flag`]: https://golang.org/pkg/flag/
  [`time.ParseDuration`]: https://golang.org/pkg/time/#ParseDuration
  [`encoding/json`]: https://golang.org/pkg/encoding/json/
  [RFC 3339]: https://tools.ietf.org/html/rfc3339
  [metodę `UnmarshalJSON`]: https://golang.org/pkg/time/#Time.UnmarshalJSON
  [`database/sql`]: https://golang.org/pkg/database/sql/
  [`gopkg.in/yaml.v2`]: https://godoc.org/gopkg.in/yaml.v2

Jeżeli nie jest możliwe aby we wspomnianych interakcjach użyć `time.Duration`, użyj `int` albo `float64` oraz dołącz jednostkę do nazwy pola.

Na przykładkl, ponieważ `encoding/json` nie wpisra `time.Duration`,
nazwa jednostki została zawarta w nazwie pola.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

Jeżeli nie jest możliwe aby we wspomnianych interakcjach użyć `time.Time`, chyba że uzgodniono inaczej, użyj `string` i formatuj znaczniki czasu (timestamps) zgodnie z definicją zawartą w [RFC 3339].

Format ten jest używany domyślnie przez [`Time.UnmarshalText`] i jest
dostępny do użytku w `Time.Format` oraz `time.Parse` poprzez [`time.RFC3339`].

  [`Time.UnmarshalText`]: https://golang.org/pkg/time/#Time.UnmarshalText
  [`time.RFC3339`]: https://golang.org/pkg/time/#RFC3339

Chociaż w praktyce nie stanowi to problemu, należy pamiętać, że
Pakiet `"time"` nie obsługuje parsowania znaczników czasu (timestamps) z sekundami przestępnymi ([8728]), ani nie uwzględnia sekund przestępnych w obliczeniach ([15190]). Jeśli porównasz dwa punkty w czasie, różnica nie obejmie sekund przestępnych, które mogły wystąpić między tymi dwoma momentami.

  [8728]: https://github.com/golang/go/issues/8728
  [15190]: https://github.com/golang/go/issues/15190

### Typy błędów

Istnieje kilka sposób deklarowania błędów:

- [`errors.New`] dla błędów z prostymi, statycznymi ciągami znaków
- [`fmt.Errorf`] dla błędów opartych o formatowalne łańcuchy znaków
- Typy niestandardowe, które implementują metodę `Error()`
- Błędy opakowane z użyciem [`"pkg/errors".Wrap`]

Podczas zwracania błędów, zadaj sobie poniższe pytania w celu wybrania opcji, która najlepiej odpowiada twoim potrzebom:

- Czy chce zwrócić prosty błąd bez żadnych dodatkowych informacji? Jeśli tak, [`errors.New`] powinno być wystarczające.
- Czy klient (twojej funkcji) powinien móc rozpoznać oraz obsłużyć ten błąd? Jeśli tak, powinieneś zastosować własny typ i zaimplementować w nim metodę `Error()`
- Czy jedynie propagujesz błąd zwracany przez inną funkcje? Jeśli tak, zobacz sekcję
na temat [opakowywania błędów](#opakowywanie-błędów-error-wrapping).
- W pozostałych przypadkach dobrym pomysłem jest użycie [`fmt.Errorf`].

  [`errors.New`]: https://golang.org/pkg/errors/#New
  [`fmt.Errorf`]: https://golang.org/pkg/fmt/#Errorf
  [`"pkg/errors".Wrap`]: https://godoc.org/github.com/pkg/errors#Wrap

Jeśli klient (twojej funkcji) potrzebuje możliwości wykrycia błędu, ale stworzyłeś błąd przy użyciu [`errors.New`], wyeksportuj błąd do oddzielnej zmiennej.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// pakiet foo

func Open() error {
  return errors.New("could not open")
}

// pakiet bar

func use() {
  if err := foo.Open(); err != nil {
    if err.Error() == "could not open" {
      // kod obsługi błędu
    } else {
      panic("unknown error")
    }
  }
}
```

</td><td>

```go
// pakiet foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// pakiet bar

if err := foo.Open(); err != nil {
  if err == foo.ErrCouldNotOpen {
    // kod obsługi błędu
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

Jeśli chcesz by twój błąd był wykrywalny przez klienta i chciałbyś dodać do niego więcej szczegółów (nie tylko pojedynczy łańcuch znaków), powinieneś utworzyć własny typ błędu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

func use() {
  if err := open("testfile.txt"); err != nil {
    if strings.Contains(err.Error(), "not found") {
      // kod obsługi błędu
    } else {
      panic("unknown error")
    }
  }
}
```

</td><td>

```go
type errNotFound struct {
  file string
}

func (e errNotFound) Error() string {
  return fmt.Sprintf("file %q not found", e.file)
}

func open(file string) error {
  return errNotFound{file: file}
}

func use() {
  if err := open("testfile.txt"); err != nil {
    if _, ok := err.(errNotFound); ok {
      // kod obsługi błędu
    } else {
      panic("unknown error")
    }
  }
}
```

</td></tr>
</tbody></table>

Uważaj z bezpośrednim eksportowaniem własnych typów błędów ponieważ stają sie one częścią publicznego API twojego pakietu. Zdecydowanie bardziej zalecane jest wystawienie funkcji które pozwolą na wykrywanie tego typu błędów (matcher functions).

```go
// pakiet foo

type errNotFound struct {
  file string
}

func (e errNotFound) Error() string {
  return fmt.Sprintf("file %q not found", e.file)
}

func IsNotFoundError(err error) bool {
  _, ok := err.(errNotFound)
  return ok
}

func Open(file string) error {
  return errNotFound{file: file}
}

// pakiet bar

if err := foo.Open("foo"); err != nil {
  if foo.IsNotFoundError(err) {
    // kod obsługi błędu
  } else {
    panic("unknown error")
  }
}
```

### Opakowywanie błędów (Error wrapping)

Istnieją trzy główne sposoby propagacji błędów w przypadku błędu wywołania funkcji:

- Zwróć oryginalny błąd jeżeli nie ma potrzeby na dodawanie żadnych dodatkowych informacji o kontekście lub gdy zależy ci na zachowaniu oryginalnego typu błędu.
- Dodawaj kontekst przy użyciu [`"pkg/errors".Wrap`] dzięki czemu komunikat błędu będzie zawierać więcej przydatnych informacji oraz [`"pkg/errors".Cause`] aby wyekstrahować oryginalną część zapakowanego błędu.
- Używaj [`fmt.Errorf`] w przypadkach, w których wiesz że dla kodu wywołującego (caller) twój kod nie zaistnieje potrzeba wykrywania oraz obsługi tego konkretnego przypadku (w przypadkach gdy wystarczy informacja że coś poszło nie tak).

Zalecane jest by dodawać kontekst tam gdzie to tylko możliwe aby zamiast niejasnych komunikatów błędów takich jak "connection refused", móc dostać przydatne informacje o kontekście wywołania takie jak w przypadku "call service foo: connection refused".

Dodając kontekst do zwracanych błędów, utrzymuj zwięzły zestaw informacji o kontekście poprzez unikanie zwrotów takich jak "failed to", które opisują rzeczy oczywiste i jedynie gromadzą się na kolejnych poziomach propagacji błędu zwiększając tym samym obiętość wynikowego komunikatu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %s", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %s", err)
}
```

<tr><td>

```go
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```go
x: y: new store: the error
```

</td></tr>
</tbody></table>

Jednakże, w przypadkach gdy błąd ma trafić do innego systemu, informacja o tym że komunikat dotyczy błędu powinna byc zawarta w wiadomości (np. poprzez tag `err` lub prefix "Failed" w logach).

Zobacz również [Don't just check errors, handle them gracefully].

  [`"pkg/errors".Cause`]: https://godoc.org/github.com/pkg/errors#Cause
  [Don't just check errors, handle them gracefully]: https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully

### Obsługa błędów asercji typów

Forma oparta na pojedynczej wartości zwracanej z [asercji typu] wywoła panikę przy podaniu nieprawidłowego typu. Dlatego staraj się wykorzystywać formę zawierającą 2 wartości zwracane z użyciem idiomu "comma ok".

  [asercji typu]: https://golang.org/ref/spec#Type_assertions

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // kod obsługi błędu.
}
```

</td></tr>
</tbody></table>

### Nie "panikuj"

Kod działający produkcyjnie musi unikać paniki. Instrukcje "panic" są głównym źródłem tzw. [cascading failures] "awarii kaskadowych". Jeśli błąd wystąpi, funkcja powinna go zwróćić i tym samym umożliwić wywołującemu zdecydowanie o ścieżce jego dalszej obsługi.  

  [cascading failures]: https://en.wikipedia.org/wiki/Cascading_failure

  <table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func foo(bar string) {
  if len(bar) == 0 {
    panic("bar must not be empty")
  }
  // ...
}

func main() {
  if len(os.Args) != 2 {
    fmt.Println("USAGE: foo <bar>")
    os.Exit(1)
  }
  foo(os.Args[1])
}
```

</td><td>

```go
func foo(bar string) error {
  if len(bar) == 0 {
    return errors.New("bar must not be empty")
  }
  // ...
  return nil
}

func main() {
  if len(os.Args) != 2 {
    fmt.Println("USAGE: foo <bar>")
    os.Exit(1)
  }
  if err := foo(os.Args[1]); err != nil {
    panic(err)
  }
}
```

</td></tr>
</tbody></table>

Panic/recover to nie strategią obsługi błędów. Program panikuje dopiero wtedy gdy błąd wynika z sytuacji nie do naprawienia takiej jak np. dereferencja wartości nil. Wyjatek od reguły stanowi tutaj etap inicjalizacji programu: błędy w procesie uruchamiania programu, które powinny przerwać program, powinny wywołać panikę.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

Nawet w testach, preferuj `t.Fatal` lub `t.FailNow` zamiast "panic" by upewnić się że test zostanie oznaczony jako nieudany.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

### Używaj go.uber.org/atomic

Operacje atomowe przeprowadzane z użyciem pakietu [sync/atomic] operują na surowych typach danych (`int32`, `int64`, itp.) a więc łatwo jest zapomnieć o użyciu operacji atomowej do odczytu lub modyfikacji zmiennych.

[go.uber.org/atomic] zwiększa bezpieczeństwo tych operacji, ukrywając typ podstawowy. Dodatkowo zawiera wygodny typ `atom.Bool`.

  [go.uber.org/atomic]: https://godoc.org/go.uber.org/atomic
  [sync/atomic]: https://golang.org/pkg/sync/atomic/

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // typ atomowy
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // juz działa...
     return
  }
  // rozpocznij foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // wyścig!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // już działa…
     return
  }
  // rozpocznij foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

### Unikaj mutowalnych zmiennych globalnych

Unikaj mutowania zmiennych globalnych, zamiast tego stosuj wstrzykiwanie zależności (dependency injection). Dotyczy to zarówno wskaźników funkcji jak i innych rodzajów wartości.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// sign.go

var _timeNow = time.Now

func sign(msg string) string {
  now := _timeNow()
  return signWithTime(msg, now)
}
```

</td><td>

```go
// sign.go

type signer struct {
  now func() time.Time
}

func newSigner() *signer {
  return &signer{
    now: time.Now,
  }
}

func (s *signer) Sign(msg string) string {
  now := s.now()
  return signWithTime(msg, now)
}
```
</td></tr>
<tr><td>

```go
// sign_test.go

func TestSign(t *testing.T) {
  oldTimeNow := _timeNow
  _timeNow = func() time.Time {
    return someFixedTime
  }
  defer func() { _timeNow = oldTimeNow }()

  assert.Equal(t, want, sign(give))
}
```

</td><td>

```go
// sign_test.go

func TestSigner(t *testing.T) {
  s := newSigner()
  s.now = func() time.Time {
    return someFixedTime
  }

  assert.Equal(t, want, s.Sign(give))
}
```

</td></tr>
</tbody></table>

# Unikaj osadzania typów (type embedding) w strukturach publicznych

Typy osadzane w strukturach publicznych powodują wyciekanie
szczegółów o implementacji, ograniczają ewolucję typów
oraz negatywnie wpływają na czytelność dokumentacji (zaciemniają ją).

Zakładając, że zaimplementowałeś różne typy list przy użyciu współdzielonego `AbstractList`, unikaj osadzania `AbstractList` w twoich konretnych implementacjach list. Zamiast tego dodaj do swojej konkretnej listy metody oddelegowujące zadanie do metod abstrakcyjnego typu listy `AbstractList`.

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
  return l.list.Add(e)
}
// Remove usuwa element z listy.
func (l *ConcreteList) Remove(e Entity) {
  return l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go pozwala na osadzanie typów (type embedding) w ramach kompromisu między dziedziczeniem a kompozycją. Typ zewnętrzny otrzymuje niejawne kopie metod typu osadzonego. Metody te domyślnie oddelegowują zadanie do metod osadzonej instacji.

  [osadzanie typów (type embedding)]: https://golang.org/doc/effective_go.html#embedding

W procesie osadzania struktura zyskuje pole o tej samej nazwie co osadzony w niej typ. Zatem jeśli typ osadzony jest publiczny, pole również będzie publiczne. W celu zachowania zgodności wstecznej, każda przyszła wersja typu zewnętrznego będzie musiała zachować typ osadzony.

Osadzony typ jest rzadko potrzebny.
To głównie wygodny sposób na uniknięcie żmudnego pisania metod oddelegowujących.

Nawet osadzenie kompatybilnego *interfejsu* `AbstractList` zamiast struktury, zapewniłby deweloperowi większą elastyczność w zakresie zmian w przyszłości, nadal jednak powodowałby wyciek informacji o tym że konkretna implementacja listy wykorzystuje implementację abstrakcyjną.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList stanowi ogólną implementację
// dla kilku typów list.
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
  list *AbstractList
}
// Add dodaje element do listy.
func (l *ConcreteList) Add(e Entity) {
  return l.list.Add(e)
}
// Remove usuwa element z listy.
func (l *ConcreteList) Remove(e Entity) {
  return l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Zarówno w przypadku osadzania struktur jak i interfejsów, osadzanie typów ogranicza ewolucję typu.

- Dodawanie metod do interfejsu osadzonego łamie kompatybilność.
- Usuwanie metod z osadzanych struktur łamie kompatybilność.
- Usuwanie typu osadzonego łamie kompatybilność.
- Wymiana typu osadzonego, nawet jeżeli zamiennik spełnia ten same interfejs, łamie kompatybilność.

*[przyp. tłum. Wspomniane wyżej "łamanie kompatybilności" odnosi się do tzw. ["breaking change"]]*  

  ["breaking change"]: https://en.wiktionary.org/wiki/breaking_change

Chociaż pisanie tych dodatkowych metod delegujących jest uciążliwe, dodatkowy wysiłek pozwala na ukrycie szczegółów implementacyjnych, pozostawia więcej miejsca na zmiany, a takze eliminuje pośrednie ujawnianie pełnego interfejsu listy w dokumentacji.

## Wydajność

Wytyczne dotyczące wydajności odnoszą się tylko to tzw. "hot paths" - ścieżek wykonywania kodu, w których spędzana jest większość czasu wykonywania i które mogą być wykonywane bardzo często.

### Preferuj strconv ponad fmt

Podczas konwersji typów prymitywnych z/do typu string, `strconv` jest szybsze od `fmt`.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  s := fmt.Sprint(rand.Int())
}
```

</td><td>

```go
for i := 0; i < b.N; i++ {
  s := strconv.Itoa(rand.Int())
}
```

</td></tr>
<tr><td>

```
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>

### Unikaj konwersji string-to-byte

Nie powtarzaj konwersji łańcuchów znakowych na wycinki bajtowe. Zamiast tego, przeprowadź operacje konwersji raz i zachowaj wynik.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  w.Write([]byte("Hello world"))
}
```

</td><td>

```go
data := []byte("Hello world")
for i := 0; i < b.N; i++ {
  w.Write(data)
}
```

</tr>
<tr><td>

```
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

### Określaj wskazówki dotyczące pojemności map

Gdy to możliwe, umieszczaj wskazówki (hints) dotyczące pojemności mapy inicjalizowanej za pomocą funkcji `make()`.

```go
make(map[T1]T2, hint)
```

Zapewnienie informacji o pojemności funkcji `make()` sprawia że ta próbuje
dopasować rozmiar mapy w czasie jej inicjalizacji, co skutkuje redukcją czasu
potrzebnego na zwiększenie mapy i alokacje pamięci podczas dodawania nowych elementów.
Pamiętaj jednak że mapy nie gwarantują obsługi wskazanej pojemności więc więc dodawanie kolejnych elementów może nadal wymagać alokacji pamięci nawet w przypadku podania wskazówki.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := ioutil.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := ioutil.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

mapa `m` stworzona bez wskazania pojemności; Może wystąpić więcej alokacji podczas operacji przypisania.

</td><td>

mapa `m` stworzona ze wskazaniem pojemności; Może wystąpić mniej alokacji podczas operacji przypisywania.

</td></tr>
</tbody></table>

## Styl

### Spójność ponad wszystko

Niektóre wytyczne przedstawione w tym dokumencie można ocenić obiektywnie;
inne zaś są sytuacyjne, kontekstowe lub subiektywne.

Ponad wszystko inne, **zachowaj spójność**.

Spójny kod łatwiej się utrzymuje oraz racjonalizuje.
Stawia on mniejszą barierę poznawczą oraz pozwala na prostrze migrowanie czy aktualizowanie wraz z pojawianiem się nowych konwencji lub w celu przeprowadzenia operacji naprawczych.

Z drugiej strony, baza kodu zawierająca wiele różnych, potencjalnie sprzecznych styli
powoduje dysonans poznawczy, zwiększa narzut związany z utrzymaniem oraz przyczynia się do wzrostu ogólnej niepewności w projekcie. Wszystko to może bezpośrednio przyczynić się do zmniejszenia prędkości pracy zespołu (velocity), bolesnego procesu review kodu oraz powstawania błędów.

Podczas stosowania tych wytycznych w swojej bazie kodu zaleca się wprowadzanie zmian
na poziomie całego pakietu (lub większym): stosowanie ich na poziomie pojedynczego "sup-package"
narusza wyżej wymienione obawy poprzez wprowadzenie wielu różnych styli w tą samą bazę kodu.

### Grupuj podobne deklaracje

Go wspiera grupowanie podobnych deklaracji.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
import "a"
import "b"
```

</td><td>

```go
import (
  "a"
  "b"
)
```

</td></tr>
</tbody></table>

Reguła ta ma również zastosowanie dla stałych, zmiennych i deklaracji typów.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go

const a = 1
const b = 2



var a = 1
var b = 2



type Area float64
type Volume float64
```

</td><td>

```go
const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)
```

</td></tr>
</tbody></table>

Grupuj jedynie związane ze sobą deklaracje. Nie grupuj deklaracji które nie mają nic wspólnego.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  ENV_VAR = "MY_ENV"
)
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const ENV_VAR = "MY_ENV"
```

</td></tr>
</tbody></table>

Grupy nie posiadają ograniczeń pod kątem miejsca użycia. Możesz użyć ich naprzykład wewnątrz ciała funkcji.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func f() string {
  var red = color.New(0xff0000)
  var green = color.New(0x00ff00)
  var blue = color.New(0x0000ff)

  ...
}
```

</td><td>

```go
func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  ...
}
```

</td></tr>
</tbody></table>

### Kolejność importowania bibliotek

Instrukcje importowania powinny być podzielone na dwie grupy:

- Biblioteka standardowa
- Cała reszta

Jest to domyślne grupowanie stosowane przez goimports.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

### Nazwy pakietów

Podczas nazywania pakietów, wybiera nazwy które:

- Posiadają jedynie małe litery. Bez wielkich liter czy podkreśleń (_).
- Nie wymagają zmiany nazwy poprzez "named imports" w większości miejsc wykorzystania.
- Są krótkie i zwięzłe. Pamiętaj że każde miejsce wywołania będzie musiało korzystać z pełnej nazwy pakietu.
- Nie zwierają liczby mnogiej. Na przykład `net/url` zamiast `net/urls`.
- Nie należą do nazw jak "common", "util", "shared" czy "lib". Są to złe, nieinformacyjne nazwy.

Zobacz również [Package Names] oraz [Style guideline for Go packages].

  [Package Names]: https://blog.golang.org/package-names
  [Style guideline for Go packages]: https://rakyll.org/style-packages/

### Nazwy funkcji

Przestrzegamy konwencji społeczności Go używającej [MixedCaps for function
names]. Wyjątkiem są funkcje testowe, które mogą zawierać podkreślenia w celu grupowania powiązanych przypadków testowych np. `TestMyFunction_WhatIsBeingTested`.

  [MixedCaps for function names]: https://golang.org/doc/effective_go.html#mixed-caps

### Aliasy importowanych bibliotek

Aliasy dla importowanych bibliotek muszą być stosowane w przypadku gdy
nazwa pakietu nie pasuje do ostatniego elemetu ścieżki importu.

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

We wszystkich innych przypadkach, import aliasing powinien być unikany chyba że
występuje konflikt w nazwach importowanych pakietów.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"


  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>

### Grupowanie i porządkowanie funkcji

- Funkcje powinny być sortowane w przybliżonej kolejności wykonywania.
- Funkcje wewnątrz tego samego pliku powinny być grupowane zgodnie z ich odbiorcą (receiver).

Dlatego najpierw w pliku powinny pojawić się funkcje eksportowane, a następnie
definicje takie jak `struct`,`const`, `var`.

Funkcje `newXYZ()`/`NewXYZ()` mogą pojawić się po definicji typu `XYZ`, jednak powinny znajdować się przed resztą metod których odbiorcą jest ten typ.

Ponieważ funkcje są pogrupowane według odbiorcy, funkcje narzędziowe ogólnego przeznaczenia powinny znajdować się na końcu pliku.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>

### Redukuj zagnieżdzenia

Kod powinien redukować zagnieżdzenia gdzie to tylko możliwe poprzez obsługę błędów oraz przypadków specialnych najpierw oraz wczesne zwrócenie lub kontynuacje pętli. Redukuj ilość kodu o wielu poziomach zagnieżdzenia.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>

### Niepotrzebne instrukcje "else"

Jeśli zmienna jest ustawiona dla obu ścieżek instrukcji if, instrukcja ta
może zostać zastąpiona pojedyńczą ścieżką instrukcji if.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>

### Deklarowanie zmiennych najwyższego poziomu

Na najwyższym poziomie (poziomie pliku), używaj standardowego słowa kluczowego `var`.
Nie określaj typu, chyba że jest on inny niżeli typ przypisywanego wyrażenia.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// Ponieważ F już informuje, że ​​zwraca łancuch znaków,
// nie musimy ponownie określać typu.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

Określ typ, jeśli typ wyrażenia nie odzwierciedla w pełni porządanego typu.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F zwraca obiekt o typie myError jednak zależy nam na typie error.
```

### Rozpoczynaj nieeksportowane zmienne globalne znakiem _

Stosuj prefix `_` dla nieeksportowanych `var` (zmiennych) oraz `const` (stałych) najwyższego poziomu żeby w przypadku użycia jasno poinformować że są symbolami globalnymi.

Wyjątek: Nieeksportowane wartości błędów, które powinny posiadać prefix `err`.

Uzasadnienie wyjątku: Zmienne i stałe najwyższego poziomu mają zakres pakietu. Używanie generycznych nazw może ułatwić popełnianie błędów polegających na przypadkowym użyciu niewłaściwej wartości w innym pliku tego samego pakietu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // Nie zobaczymy błędu kompilacji po usunięciu pierwszej lini w funkcji Bar.
  // Zamiast tego zostanie użyta globalna stała o tej samej nazwie.
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

### Zagnieżdzanie w strukturach (embedding)

Typy zagnieżdzone (takich jak mutexy) powinny występować na samej górze
listy pól struktury w której są zagnieżdzane. Powinny być również oddzielone pojedynczą pustą linią od zwykłych pól.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

### Jawnie podawaj nazwy pól podczas inicjalizacji struktur

Prawie zawsze powinieneś jawnie podawać nazwy pól podczas inicjalizowania struktur.
To jest teraz wymuszane przez polecenie [`go vet`].

  [`go vet`]: https://golang.org/cmd/vet/

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

Wyjątek: Nazwy pól **mogą w ostateczności** być pomijane w przypadku tablic testowych składających się z 3 lub mniejszej ilości pól.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

### Deklarowanie zmiennych lokalnych

Skrócone deklaracje zmiennych (`:=`) powinny być używane gdy operacja przyporządkowywania wartości do zmiennej jest jawna.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

Jednakże, są przypadki w których domyślna wartość zmiennej staje się bardziej oczywista gdy zastosuje się słowo kluczowe `var`. Jak w przypadku [deklarowania pustych wycinków].

  [deklarowania pustych wycinków]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>

### "nil" to poprawna wartość wycinka

`nil` to poprawna wartość wycinka o zerowej długości. Oznacza to że:

- Nie powinieneś zwracać wycinków o zerowej długości jawnie. Zamiast tego zwróć `nil`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- Aby sprawdzić czy wycinek jest pusty, zawsze stosuj wyrażenie `len(s) == 0`. Nie porównuj go z wartością `nil`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- Wartość zerowa (wycinek zadeklarowany przy użyciu `var`) nadaje się do użytku od razu, bez konieczności stosowania funkcji `make()`.

  <table>
  <thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // lub, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

### Redukuj zasięg (scope) zmiennych

Jeśli to tylko możliwe, staraj się redukować zakres (scope) zmiennych.
Nie redukuj zakresu jeśli stoi to w sprzeczności z zasadami opisanymi w [Redukuj zganieżdzenia](#redukuj-zagnieżdzenia)

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
err := ioutil.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := ioutil.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

Jeżeli potrzebujesz wyniku wywołania funkcji poza instrukcją if, nie powinieneś starać się redukować zasięgu.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := ioutil.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}
```

</td><td>

```go
data, err := ioutil.ReadFile(name)
if err != nil {
   return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil
```

</td></tr>
</tbody></table>

### Unikaj przekazywania "surowych" wartości parametrów

Surowe wartości parametrów przekazywane podczas wywoływania funkcji mogą zaszkodzić czytelności.
Dodawaj komentarze w stylu języka C (`/* ... */`) z nazwami parametrów jeśli ich znaczenie nie jest oczywiste.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

Jeszcze lepiej będzie gdy zastąpisz "surową" wartość boolowską własnym typem w celu poprawienia czytelności oraz kodu bezpiecznego pod kątem typowania. Zachowanie takie zezwala na potencjalne przekazywanie przez ten parametr więcej niż dwóch stanów jeśli w przyszłości zajdzie taka potrzeba.

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady = iota + 1
  StatusDone
  // Może w przyszłości dodamy kolejny możliwy status - StatusInProgress.
)

func printInfo(name string, region Region, status Status)
```

### Używaj literałów znakowych (string literals) w celu uniknięcia znaków ucieczki

Go wspiera [surowe literały znakowe (raw string literals)](https://golang.org/ref/spec#raw_string_lit), które mogą obejmować wiele wierszy i zawierać znaki cudzysłowu.
Używaj ich by uniknąć ręcznego dodawania znaków ucieczki (escapingu), co zdecydowanie zmniejsza czytelność.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>

### Inicjalizowanie referencji do struktur

Używaj `&T{}` zamiast `new(T)` kiedy inicjalizujesz referencje do struktury tak aby było to zgodne z inicjalizacją samych struktur.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// niespójne
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>

### Inicjalizowanie map

Preferuj `make(..)` dla pustych map oraz map zapełnianych w czasie działania programu.
To sprawia, że inicjalizacja mapy różni się wizualnie od jej deklaracji i ułatwia późniejsze dodawanie wskazówek dotyczących jej rozmiaru, jeśli to możliwe.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 jest bezpieczne dla operacji odczytu oraz zapisu
  // m2 wywoła panikę przy próbie zapisu
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 jest bezpieczne dla operacji odczytu oraz zapisu
  // m2 wywoła panikę przy próbie zapisu
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

Deklaracja oraz inicjalizacja są podobne wizualnie.

</td><td>

Deklaracja oraz inicjalizacja są odróżnialne wizualnie.

</td></tr>
</tbody></table>

Tam, gdzie to możliwe, stosuj wskazówki dotyczące pojemności podczas inicjalizacji z użyciem funkcji `make()`. Więcej szczegółów znajdziesz w rozdziale [Określaj wskazówki dotyczące pojemności map](#określaj-wskazówki-dotyczące-pojemności-map).

Z drugiej strony, jeśli mapa zawiera z góry ustalone elementy,
do jej inicjalizacji użyj literału.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

Podstawową zasadą jest używanie literałów mapy podczas dodawania stałego zestawu
elementów w czasie inicjalizacji, w przeciwnym wypadku użyj funkcji `make` (i podawaj wskazówki dotyczące rozmiaru jeżeli to możliwe).

### Formatuj łańcuchy znakowe poza funkcją "Printf"

Jeśli deklarujesz formatowalne łańcuchy znakowe w stylu funkcji `Printf` twórz z nich stałe przy użyciu `const`.

Pomoże to poleceniu `go vet` w wykonaniu statycznej analizy formatowania wewnątrz tego łańcucha.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>

### Nazewnictwo funkcji "Printf-style"

Kiedy deklarujesz funkcje w stylu funkcji `Printf`, upewnij się że `go vet` jest w stanie ją wykryć oraz sprawdzić wewnętrzny łańcuch znakowy zawierający formatowanie.

Oznacza to że powinieneś używać predefiniowanych nazw w stylu `Printf` jeżeli to tylko możliwe. `go vet` domyślnie sprawdza takie funkcje. W celu poznania szczegółów zobacz [Printf family].  

  [Printf family]: https://golang.org/cmd/vet/#hdr-Printf_family

Jeżeli używanie predefiniowanych nazw nie jest możliwe, zakońćż wybraną nazwę literą "f" nazywają funkcję `Wrapf` (nie `Wrap`). `go vet` posiada opcje pozwalające na wyspecyfiukowanie funkcji w stylu `Printf` jednak ich nazwy muszą kończyć się literą "f".

```shell
$ go vet -printfuncs=wrapf,statusf
```

Zobacz również artykuł [go vet: Printf family check].

  [go vet: Printf family check]: https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/

## Wzorce

### Tablice testowe

Stosuj testy oparte o tablice testowe z wykorzystaniem [subtests] aby uniknąć powielania kodu w przypadku gdy logika testów jest powtarzalna.

  [subtests]: https://blog.golang.org/subtests

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

Postępujemy zgodnie z konwencją mówiącą że wycinki struktur testowych należy nazwać `tests` a każdy scenariusz testowy `tt`. Ponadto zachęcamy do jawnego oznaczania danych wejściowych oraz wyjściowych dla każdego scenariusza testowego prefiksami `give` oraz `want`.

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

### Opcje funkcjonalne

Opcje funkcjonalne to wzorzec, w którym deklarujesz przykrywający typ `Option`, który rejestruje informacje w pewnej nieeksportowanej (wewnętrznej, prywatnej) strukturze. Akceptujesz dowolną ilość takich opcji i działasz na podstawie pełnej informacji zawartej przez opcje w tej strukturze.

Użyj tego wzorca dla opcjonalnych argumentów w konstruktorach i innych publicznych interfejsach API, szczególnie gdy przewidujesz późniejszą potrzebę ich rozszerzania, zwłaszcza jeśli masz już funkcje z trzema lub większą ilością argumentów.

<table>
<thead><tr><th>Źle</th><th>Dobrze</th></tr></thead>
<tbody>
<tr><td>

```go
// pakiet db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// pakiet db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open otwiera połączenie.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

"cache" oraz "logger" są zawsze wymagane
nawet jeżeli użytkownik chce użyć ich wartości domyślnych.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

Opcje można podać tylko w razie potrzeby.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

Sugerowany przez nas sposób implementacji tego wzorca opiera się o interfejs `Option` zawierjaący nieeksportowaną metodę rejestrującą opcje w nieexportowanej strukturze `options`.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open otwiera połączenie.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

Warto zaznaczyć że istnieje sposób implementacji tego wzorca oparty na domnięciach
jednak jesteśmy zdania że powyższy wzorzec zapewnia autorowi większą elastyczność
i jest łatwiejszy w debugowaniu i testowaniu przez użytkowników. Sposób ten pozwala w szczególności na porównywanie opcji między sobą w testach oraz imitacjach (mock'ach) co w przypadku domknięć staje się niemożliwe. Ponad to pozwala opcjom na implementowanie innych interfejsów takich jak `fmt.Stringer`, który umożliwia dodanie opcjom reprezentacji czytelnej dla użytkownika.

Zobacz również,

- [Self-referential functions and the design of options]
- [Functional options for friendly APIs]

  [Self-referential functions and the design of options]: https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html
  [Functional options for friendly APIs]: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis
