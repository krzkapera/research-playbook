# Flow koordynacji badawczej

Ten plik dotyczy ról `decision-maker`, `researcher`, `critic` i `experiment-designer`. Implementer, operator i analyst czytają go tylko wtedy, gdy ich zlecenie wymaga decyzji badawczej.

```text
decision-maker: propozycja hipotezy
  <-> researcher: krytyka, podstawy i alternatywy
 decision-maker: decyzja o hipotezie
  -> experiment-designer: propozycje eksperymentu
  <-> critic: krytyka projektu i alternatywy
 experiment-designer/decision-maker: decyzja o eksperymencie
  -> implementer: wykonanie
  <-> critic: krytyka implementacji i bugi
  -> operator: uruchomienie i joby
  -> analyst: kontrola oraz analiza wyników
  -> researcher: interpretacja względem hipotezy
  -> decision-maker: kontynuacja, zmiana, zamknięcie albo eskalacja
```

To opis przepływu odpowiedzialności, nie sztywny automat. Każdy agent może zatrzymać krok, zgłosić błąd lub poprosić o zmianę zakresu. Decyzja o hipotezie i decyzja o eksperymencie muszą być jawne w kanale i pliku.

Jedna hipoteza może mieć wiele eksperymentów-dzieci, a wiele hipotez i eksperymentów może być prowadzonych równolegle. Nie tworzymy osobnych zasad dla „parallel experiments”.

## Gdy critic jest nieobecny

Krytyka nie jest zadaniem z właścicielem, tylko luźną dyskusją na kanale (patrz `file-lifecycle.md`). Wnosi ją każdy aktualnie obecny agent, zgodnie ze swoją rolą. Gdy akurat nikt nie pełni roli `critic`, po prostu nikt nie wnosi tej perspektywy — nie ma formalnego przejęcia ani zastępstwa. Jedynym stałym elementem pracy są hipoteza i eksperyment same w sobie; to ich obecność i stan są śledzone, nie obecność konkretnych ról.
