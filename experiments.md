# Eksperymenty

Eksperyment to węzeł drzewa `orx`, dziecko hipotezy, którą testuje (`orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` — `id` hipotezy, nie jej slug, patrz `common/identifiers.md`). Ma prawdziwy branch i własne runy. Na Slurmie job startuje z pliku `job.sbatch` w korzeniu brancha (`orx exp run --backend slurm` — patrz `roles/programmer.operator.md`); pole `run_command` węzła nie jest do tego używane i nie ustawia się go przy tworzeniu węzła.

Treść eksperymentu — pytanie, ustalenia, krytyka, wynik — żyje w jego `description`, edytowanym przez `orx exp desc`. Surowe logi i wyniki runów zostają tam, gdzie `orx` je zapisuje (`orx logs <run-id>`); `description` je streszcza i wskazuje, nie duplikuje.


## Kanał

Każdy aktywny eksperyment ma kanał `ai-crew-sync` nazwany jego slugiem. Zakłada go agent tworzący węzeł (zazwyczaj `laborant`), zaraz po `orx create-experiment`, i ogłasza powstanie na kanale hipotezy-rodzica. Szczegóły dołączania: `common/communication.md`.

## Zawartość opisu

Nie ma ustalonej listy pól — dobierz treść (pytanie, status, baseline, metryki, zasoby, cokolwiek akurat istotne) tak, żeby opis był samowystarczalny (patrz `common/communication.md`). Brak jakiegoś elementu oznacza „nieustalone albo niepotrzebne w tym przypadku", nie brakujący wymóg.

## Stan eksperymentu

Nie ma ustalonej listy nazw stanów ani wymuszonych przejść między nimi — opisz bieżący stan swobodnym tekstem w `description`. Jedyna stała zasada (patrz `common/rules.md`): `orx` mówi tylko, czy run się wykonał (`Starting/Running/Done/Failed/Cancelled`), nie czy wynik jest naukowo sensowny — rozróżnienie błędu infrastruktury, błędu implementacji i właściwego wyniku naukowego zapisujemy sami, i błąd infrastruktury/implementacji nigdy nie liczy się jako wynik wspierający ani obalający hipotezę.

## Krytyka

Przed, w trakcie i po eksperymencie każdy agent może zgłosić problem — w kanale, nie jako zadanie z właścicielem (patrz `common/communication.md`, "Zadanie czy dyskusja"). Krytyka dotyczy także eksperymentów już zakończonych. Wynik może zostać uznany za nieinterpretowalny, jeśli błąd uniemożliwia odpowiedź na pytanie.

## Równoległe warianty

Warianty to rodzeństwo w drzewie — wspólny `parent_experiment_id`, osobne slugi. Mogą być uruchamiane równolegle, jeśli różnice są jawne i nie powodują pomieszania artefaktów. Nie zakładamy, że wszystkie eksperymenty w hipotezie tworzą macierz parametrów.
