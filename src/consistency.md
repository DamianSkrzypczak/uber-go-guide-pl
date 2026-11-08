# Spójność ponad wszystko

Niektóre wytyczne przedstawione w tym dokumencie można ocenić obiektywnie, 
inne są sytuacyjne, kontekstowe lub subiektywne.

Ponad wszystko inne: **zachowaj spójność**.

Spójny kod łatwiej się utrzymuje, łatwiej analizuje, 
wymaga mniejszego nakładu poznawczego i ułatwia migrację 
oraz aktualizację wraz z pojawianiem się nowych konwencji lub naprawianiem błędów.

Z drugiej strony baza kodu zawierająca wiele różnych, 
sprzecznych stylów powoduje narzut utrzymaniowy, 
niepewność i dysonans poznawczy, co przekłada się bezpośrednio na spadek prędkości pracy zespołu, 
uciążliwe przeglądy kodu (code review) i powstawanie błędów.

Podczas stosowania tych wytycznych zaleca się wprowadzanie zmian na poziomie całego pakietu (lub większym) – 
stosowanie ich na poziomie pojedynczego podpakietu wprowadza mieszankę stylów w tej samej bazie kodu.