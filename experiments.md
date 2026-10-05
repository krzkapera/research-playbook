# Eksperymenty

Główny eksperyment hipotezy uruchamiaj bezpośrednio na węźle hipotezy; nie twórz dla niego sztucznego dziecka. Dodatkowe, odrębne pytania testuj w eksperymentach-dzieciach bezpośrednio pod węzłem hipotezy: `orx create-experiment <project_id> --parent <id-hipotezy> --run-command 'cd repo 2>/dev/null; bash run.sh' --title "..."` (`id` hipotezy, nie slug; `identifiers.md`). Komenda wypisuje `id` i slug (linia `slug:`); dziecko ma własny branch `orx/<slug>` i runy. Po `NEXT_TEST` laborant tworzy właśnie takie dziecko, zapisuje w nim protokół i przygotowuje brief dla jego sluga; przypisany koder kontynuuje w tej samej sesji.

Na Slurmie job startuje z `job.sbatch` w korzeniu brancha (`orx exp run --backend slurm`), a na `home` z komendy runu węzła; oba wywołują `run.sh` (`roles/operator.md` § `job.sbatch`). Dlatego każdy węzeł tworzysz z dokładnie tą komendą runu: `--run-command 'cd repo 2>/dev/null; bash run.sh'`.

Po przyjęciu wyniku przez laboranta (`RESEARCH_REPORT`) branch węzła jest zamrożony: nie commitujesz na nim więcej. Dalsza zmiana to nowy eksperyment-dziecko.

## description i logi

Treść (pytanie, protokół eksperymentu, kryterium sukcesu, ustalenia, krytyka, wynik) żyje w `description` (`orx exp desc`). Surowe logi i wyniki runów zostają tam, gdzie zapisuje je `orx` (`orx logs <run-id>`); `description` je streszcza i wskazuje ścieżki (`identifiers.md` § Miejsca zapisu).

`description` jest nadpisywane w całości. Professor zapisuje twierdzenie hipotezy, wnioski i jej stan; laborant zapisuje uzgodniony protokół eksperymentu, krytykę i zweryfikowane ustalenia naukowe. Szczegółowy plan implementacji jest przechowywany w briefie kodera w artifacts, nie w `description`. Każdy zapis `description` wymaga kooperacyjnego locka `orx-desc:<project_id>:<node_id>` z `communication.md` § Opis węzła vs wpis: acquire → świeży odczyt → zmiana z zachowaniem treści innych autorów → zapis → release. Nie zapisuj treści odczytanej przed uzyskaniem locka. Po utracie dzierżawy odrzuć kopię i zacznij od świeżego odczytu.

W `description` zapisuj to, co istotne dla eksperymentu i hipotezy (pytanie, ustalenia, wynik względem hipotezy). Przebieg runu (`Starting` / `Running` / `Done` / `Failed` / `Cancelled`) zostaje w raporcie `orx`.

## Kanał

Eksperyment główny uruchomiony na węźle hipotezy używa kanału hipotezy, założonego przez professora. Eksperyment-dziecko ma kanał nazwany swoim slugiem; zakłada go laborant po utworzeniu węzła, według `communication.md` § Kanały.

## Warianty równoległe

Warianty to rodzeństwo: wspólny rodzic, osobne slugi i ścieżki artefaktów. Uruchamiaj równolegle, gdy różnice są jawne.
