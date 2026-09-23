# Eksperymenty

Eksperyment to węzeł drzewa `orx`, dziecko hipotezy, którą testuje: `orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` (`id` hipotezy, nie slug — `common/identifiers.md`). Ma własny branch i runy.

Na Slurmie job startuje z `job.sbatch` w korzeniu brancha (`orx exp run --backend slurm`; patrz `roles/programmer.operator.md`). Pola `run_command` nie ustawia się przy tworzeniu węzła.

## description i logi

Treść (pytanie, ustalenia, krytyka, wynik) żyje w `description` (`orx exp desc`). Surowe logi i wyniki runów zostają tam, gdzie zapisuje je `orx` (`orx logs <run-id>`); `description` je streszcza i wskazuje ścieżki, nie duplikuje.

`description` jest nadpisywane w całości: przed zapisem odczytaj bieżącą treść (`orx exp status` / `orx exp desc`) i zapisz pełną zaktualizowaną wersję. Edytuje wyłącznie aktualny właściciel etapu (`laborant`); programmer i operator oddają ścieżki oraz status na kanale.

Rozróżniaj w `description`: błąd infrastruktury, błąd implementacji, wynik naukowy. `orx` raportuje tylko przebieg runu (`Starting` / `Running` / `Done` / `Failed` / `Cancelled`). Tylko wynik naukowy wspiera albo obala hipotezę. Gdy błąd uniemożliwia odpowiedź na pytanie, wynik jest nieinterpretowalny.

## Kanał

Każdy aktywny eksperyment ma kanał `ai-crew-sync` nazwany jego slugiem. Zakłada go agent tworzący węzeł (zwykle `laborant`), zaraz po utworzeniu, i ogłasza to na kanale hipotezy-rodzica (`common/communication.md`).

## Warianty równoległe

Warianty to rodzeństwo: wspólny rodzic, osobne slugi i ścieżki artefaktów. Uruchamiaj równolegle, gdy różnice są jawne.
