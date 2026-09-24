# Eksperymenty

Eksperyment to węzeł drzewa `orx`, dziecko hipotezy, którą testuje: `orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` (`id` hipotezy, nie slug; `identifiers.md`). Komenda wypisuje `id` i slug (linia `slug:`). Eksperyment ma własny branch `orx/<slug>` i runy.

Na Slurmie job startuje z `job.sbatch` w korzeniu brancha (`orx exp run --backend slurm`; `roles/operator.md`). Pola `run_command` nie ustawia się przy tworzeniu węzła.

## description i logi

Treść (pytanie, design, kryterium sukcesu, ustalenia, krytyka, wynik) żyje w `description` (`orx exp desc`). Surowe logi i wyniki runów zostają tam, gdzie zapisuje je `orx` (`orx logs <run-id>`); `description` je streszcza i wskazuje ścieżki (`identifiers.md` § Miejsca zapisu).

`description` jest nadpisywane w całości: przed zapisem odczytaj bieżącą treść (`orx exp status` / `orx exp desc`) i zapisz pełną zaktualizowaną wersję. Edytuje wyłącznie `laborant`; materiał od innych ról przychodzi na kanale eksperymentu.

W `description` zapisuj to, co istotne dla eksperymentu i hipotezy (pytanie, ustalenia, wynik względem hipotezy). Przebieg runu (`Starting` / `Running` / `Done` / `Failed` / `Cancelled`) zostaje w raporcie `orx`.

## Kanał

Każdy aktywny eksperyment ma kanał nazwany jego slugiem. Zakłada go `laborant` po utworzeniu węzła, według `communication.md` § Kanały.

## Warianty równoległe

Warianty to rodzeństwo: wspólny rodzic, osobne slugi i ścieżki artefaktów. Uruchamiaj równolegle, gdy różnice są jawne.
