# Eksperymenty

Eksperyment to węzeł drzewa `orx`, dziecko hipotezy, którą testuje (`orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` — `id` hipotezy, nie jej slug, patrz `common/identifiers.md`). Ma prawdziwy branch, `run_command` i własne runy.

Treść eksperymentu — pytanie, ustalenia, krytyka, wynik — żyje w jego `description`, edytowanym przez `orx exp desc`. Surowe logi i wyniki runów zostają tam, gdzie `orx` je zapisuje (`orx logs <run-id>`); `description` je streszcza i wskazuje, nie duplikuje.

## Minimalna zawartość opisu

- pytanie, na które eksperyment ma odpowiedzieć;
- aktualny status (patrz niżej);
- właściciel bieżącego kroku;
- odniesienie do runów (`orx runs`) i artefaktów.

## Gdy potrzebne

Dodajemy: baseline, zmienną, stałe, dataset, split, liczbę przykładów, seed, powtórzenia, metryki, kryterium porównania, zasoby HPC i plan analizy. Brak pola oznacza „nieustalone albo niepotrzebne", a nie domyślną wartość.

## Stany eksperymentu

```text
PROPOSED -> DISCUSSING -> READY -> IMPLEMENTING -> RUNNING
RUNNING -> ANALYZING -> COMPLETED
IMPLEMENTING -> BLOCKED | INVALID
RUNNING -> FAILED_INFRASTRUCTURE | FAILED_IMPLEMENTATION | BLOCKED
ANALYZING -> REPEAT | INVALID | COMPLETED
```

To nasza warstwa nad surowym statusem runu z `orx` (`Starting/Running/Done/Failed/Cancelled`): `orx` mówi, czy run się wykonał, nie czy wynik jest naukowo sensowny. Rozróżnienie infrastruktura/implementacja/wynik naukowy zapisujemy sami w `description`. `FAILED_INFRASTRUCTURE` i `FAILED_IMPLEMENTATION` nie są wynikami wspierającymi ani obalającymi hipotezę.

## Krytyka

Przed, w trakcie i po eksperymencie każdy agent może zgłosić problem — w kanale, nie jako zadanie z właścicielem (patrz `coordination-flow.md`). Krytyka dotyczy także eksperymentów już zakończonych. Wynik może zostać uznany za nieinterpretowalny, jeśli błąd uniemożliwia odpowiedź na pytanie.

## Równoległe warianty

Warianty to rodzeństwo w drzewie — wspólny `parent_experiment_id`, osobne slugi. Mogą być uruchamiane równolegle, jeśli różnice są jawne i nie powodują pomieszania artefaktów. Nie zakładamy, że wszystkie eksperymenty w hipotezie tworzą macierz parametrów.
