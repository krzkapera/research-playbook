# Flow koordynacji badawczej

Ten plik opisuje przepływ przez domeny wewnątrz person: `decision-maker` to professor na poziomie hipotezy i laborant na poziomie eksperymentu (patrz `professor-laborant.decision-maker.md`), `researcher` to professor, `experiment-designer`/`analyst` to laborant, `implementer`/`operator` to programmer, `critic` to osobna persona critic. Programmer czyta ten plik tylko wtedy, gdy jego zlecenie wymaga decyzji badawczej, nie do zwykłej implementacji.

```text
professor/decision-maker: propozycja hipotezy
  <-> professor/researcher: krytyka, podstawy i alternatywy
 professor/decision-maker: decyzja o hipotezie
  -> laborant/experiment-designer: propozycje eksperymentu
  <-> critic: krytyka projektu i alternatywy
 laborant/decision-maker: decyzja o eksperymencie
  -> programmer/implementer: wykonanie
  <-> critic: krytyka implementacji i bugi
  -> programmer/operator: uruchomienie i joby
  -> laborant/analyst: kontrola oraz analiza wyników
  -> professor/researcher: interpretacja względem hipotezy
  -> decision-maker (professor albo laborant, zależnie od poziomu): kontynuacja, zmiana, zamknięcie albo eskalacja
```

To opis przepływu odpowiedzialności, nie sztywny automat. Każdy agent może zatrzymać krok, zgłosić błąd lub poprosić o zmianę zakresu. Decyzja o hipotezie i decyzja o eksperymencie muszą być jawne w kanale i pliku.

Jedna hipoteza może mieć wiele eksperymentów-dzieci, a wiele hipotez i eksperymentów może być prowadzonych równolegle. Nie tworzymy osobnych zasad dla „parallel experiments”.

## Gdy critic jest nieobecny

Krytyka nie jest zadaniem z właścicielem, tylko luźną dyskusją na kanale (patrz `file-lifecycle.md`). Wnosi ją każdy aktualnie obecny agent, zgodnie ze swoją rolą. Gdy akurat nikt nie pełni roli `critic`, po prostu nikt nie wnosi tej perspektywy — nie ma formalnego przejęcia ani zastępstwa. Jedynym stałym elementem pracy są hipoteza i eksperyment same w sobie; to ich obecność i stan są śledzone, nie obecność konkretnych ról.
