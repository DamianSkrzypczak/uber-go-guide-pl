# Odbiorniki (Receivers) i Interfejsy

Metody przynależące do wartości odbiornika mogą być wywoływane zarówno na jego wskaźnikach, jak i wartościach.
Metody przynależące do wskaźnika odbiornika (pointer receiver) mogą być wywoływane jedynie na jego wskaźnikach lub [wartościach adresowalnych](https://go.dev/ref/spec#Method_values).

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

// Nie możemy uzyskać wskaźników do wartości przechowywanych w mapach,
// ponieważ nie są one wartościami adresowalnymi.
sVals := map[int]S{1: {"A"}}

// Możemy wywołać Read na wartościach przechowywanych w mapie, ponieważ Read
// ma odbiornik wartościowy, który nie wymaga adresowalności.
sVals[1].Read()

// Nie możemy wywołać Write na wartościach przechowywanych w mapie, ponieważ Write
// ma odbiornik wskaźnikowy, a uzyskanie wskaźnika do elementu mapy jest niemożliwe.
//
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// Możesz wywoływać zarówno Read, jak i Write, jeśli mapa przechowuje wskaźniki,
// ponieważ wskaźniki są z natury adresowalne.
sPtrs[1].Read()
sPtrs[1].Write("test")
```

Podobnie, interfejs może zostać spełniony poprzez wskaźnik, nawet jeśli odbiornikiem zadeklarowanej metody jest wartość, a nie wskaźnik.

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// Poniższy kod się nie skompiluje, ponieważ s2Val jest wartością, a brak odbiornika wartościowego dla f.
//   i = s2Val
```

Efektywny Go dobrze opisuje ten przypadek w rozdziale [Pointers vs. Values](https://go.dev/doc/effective_go#pointers_vs_values).
