# Eksperymenty

Eksperyment to węzeł drzewa `orx`, dziecko hipotezy, którą testuje (`orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` — `id` hipotezy, nie jej slug, patrz `common/identifiers.md`). Ma prawdziwy branch i własne runy. Na Slurmie job startuje z pliku `job.sbatch` w korzeniu brancha (`orx exp run --backend slurm` — patrz `roles/programmer.operator.md`); pole `run_command` węzła nie jest do tego używane i nie ustawia się go przy tworzeniu węzła.

Treść eksperymentu — pytanie, ustalenia, krytyka, wynik — żyje w jego `description`, edytowanym przez `orx exp desc`. Surowe logi i wyniki runów zostają tam, gdzie `orx` je zapisuje (`orx logs <run-id>`); `description` je streszcza i wskazuje, nie duplikuje.

Pole `description` jest nadpisywane w całości przy każdej zmianie: przed edycją odczytaj bieżącą treść (`orx exp status`/`orx exp desc`) i zapisz pełną, zaktualizowaną wersję, nie tylko dopisek. Edytuje je wyłącznie aktualny właściciel etapu (`laborant`); programmer i operator przekazują ścieżki i status na kanale.


## Kanał

Każdy aktywny eksperyment ma kanał `ai-crew-sync` nazwany jego slugiem. Zakłada go agent tworzący węzeł (zazwyczaj `laborant`), zaraz po `orx create-experiment`, i ogłasza powstanie na kanale hipotezy-rodzica. Szczegóły dołączania: `common/communication.md`.


## Zawartość opisu

Dobierz treść opisu (pytanie, status, baseline, metryki, zasoby, cokolwiek akurat istotne) tak, żeby był samowystarczalny (patrz `common/communication.md`). Wpisuj elementy potrzebne w tej chwili; pominięcie elementu oznacza, że jest nieustalony albo zbędny w tym przypadku.

## Stan eksperymentu

Bieżący stan opisz swobodnym tekstem w `description`. Stała zasada (patrz `common/rules.md`): `orx` raportuje wykonanie runu (`Starting/Running/Done/Failed/Cancelled`); sens naukowy wyniku zapisujesz Ty w `description`, z jawnym rozróżnieniem błędu infrastruktury, błędu implementacji i właściwego wyniku naukowego. Tylko wynik naukowy liczy się jako wsparcie albo obalenie hipotezy.

## Krytyka

Przed, w trakcie i po eksperymencie każdy agent może zgłosić problem — w kanale, nie jako zadanie z właścicielem (patrz `common/communication.md`, "Zadanie czy dyskusja"). Krytyka dotyczy także eksperymentów już zakończonych. Wynik może zostać uznany za nieinterpretowalny, jeśli błąd uniemożliwia odpowiedź na pytanie.

## Równoległe warianty

Warianty to rodzeństwo w drzewie — wspólny `parent_experiment_id`, osobne slugi. Uruchamiaj je równolegle, gdy różnice są jawne i artefakty mają osobne ścieżki. Każdy eksperyment w hipotezie może mieć własny kształt — macierz parametrów jest opcją, nie domyślnym wzorcem.
