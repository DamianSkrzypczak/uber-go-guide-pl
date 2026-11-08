# Wskaźniki do interfejsów

Praktycznie nigdy nie ma potrzeby używania wskaźników do interfejsów. Powinieneś przekazywać interfejs poprzez wartość – w dalszym ciągu możliwe jest, aby przekazywany obiekt był wskaźnikiem na dane.

Interfejs składa się z dwóch pól:

1. Wskaźnik na pewne informacje specyficzne dla typu. Możesz myśleć o tym jako o "typie".
2. Wskaźnik na dane. Jeśli przechowywaną wartością jest wskaźnik, jest on zapisywany bezpośrednio. W innym przypadku, gdy wartością są dane, przechowywany zostaje wskaźnik na te dane.

Jeśli więc chcesz, aby metody typu interfejsowego modyfikowały jego dane, musisz w nich używać wskaźnika.
