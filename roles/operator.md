# Procedura obsługi jobów dla roli programmer

Ten plik zawiera procedury operacyjne stosowane przez programmera, który sam wykonuje eksperyment. Nie opisuje osobnej sesji ani przydziału agenta: programmer czyta go razem z `roles/programmer.md` i pozostaje właścicielem kodu, brancha oraz joba.

Wszystkie uruchomienia wykonuj przez `orx exp run`: na klastrach backendem Slurm z `job.sbatch`, na `home` backendem ssh (sekcja `home`). Nie uruchamiaj treningu bezpośrednio przez `sbatch`, `ssh` ani poza ORX.

## Pojęcia

- **Eksperyment** — węzeł wskazany w briefie kodera; brief podaje `project_id`, `node_id` i slug (`experiments.md`).
- **`description`** — źródło prawdy o pytaniu, uzgodnionym designie i ograniczeniach naukowych. Przed submitem czytaj jego aktualną treść.
- **`remoteRoot`** — katalog ORX określony w konfiguracji Slurm (domyślnie `~/scratch/.orx`): snapshoty w `source/`, runy w `runs/<runId>/`.
- **Smoke** — początkowy krótki test implementacji, przed pełnym uruchomieniem. Po udanym smoke nie powtarzaj go dla tego eksperymentu, także po poprawkach kodu lub wznowieniu.

## Wybór hosta i zasobów

1. Dobierz host do wymagań eksperymentu i aktualnej kolejki; uwzględnij dokumentację Cyfronetu oraz wzorce `.sh` i `.sbatch` z `~/scratch/`. Dla Heliosa uwzględnij ARM i jego właściwy profil.
2. Ares wybierz, gdy wystarcza CPU; nie kieruj tam jobów wymagających GPU. Athenę wybierz dla GPU, gdy jej zasoby wystarczą. Heliosa wybierz dla pełnych datasetów lub cięższych jobów. Uwzględnij też inne joby użytkownika.
3. `home` to komputer użytkownika z GPU (RTX), bez kolejki. Rozważ go dla małych obliczeń few-shot, które policzą się w kilka minut, a nie są wielowariantowym batchem — wtedy nie ma sensu czekać w kolejce klastra. Przed użyciem wyślij orchestratorowi P2P `HOME_ACCESS_REQUEST` (projekt, węzeł, krótki opis obciążenia i czasu) i zakończ turę. `home` wybierz dopiero po `HOME_ACCESS_ANSWER` z wyraźną zgodą użytkownika i potwierdzeniem, że komputer jest dostępny; sama dostępność SSH nie jest zgodą.
4. Zasoby i timelimit bierz tak, żeby wystarczyły, ale jak najmniejsze — wtedy job szybciej wychodzi z kolejki. Nie oszczędzaj, ale nie bierz na zapas. Utrzymuj wysoką efficiency widoczną w `hpc-jobs`, żeby nie marnować grantu, ale nie duś GPU: jeśli przy większej liczbie CPU job policzy się wyraźnie szybciej, weź więcej CPU — przede wszystkim liczy się szybkość otrzymania wyników. Zgłaszaj joby często, ale uzasadnione; nie zastępuj myślenia masowymi eksperymentami. Nie anuluj cudzych zadań.
5. Umieszczaj projekty, środowiska, cache, datasety, checkpointy, wyniki i logi na klastrze w `~/scratch/<nazwa-projektu>`; pliki ORX runu pozostają w `remoteRoot/runs/<runId>/`. Transferuj duże pliki bez pośredniego zapisywania na hoście agentów. Dane lokalnego `home`, jeśli użytkownik zatwierdził obliczenie, trzymaj w `~/research/<slug>/`.

## Przygotowanie i uruchomienie

1. Przed pierwszym jobem HPC przeczytaj dokumentację hosta oraz lokalne wzorce skryptów. Brief kodera i aktualny `description` określają wejścia, zależności, design, metryki oraz wymagane wyniki.
2. Przygotuj `job.sbatch` na branchu `orx/<slug>` i zacommituj go wraz z implementacją przed smoke/submitem. Job musi być wznawialny i zapisywać logi oraz wyniki w odrębnych lokalizacjach runu.
3. Wykonaj smoke na początku, tuż po przygotowaniu implementacji, przez `orx exp run`. Zapisz jego `run_id`. Po sukcesie nie powtarzaj smoke dla tego eksperymentu. Po błędzie ustal przyczynę, popraw implementację lub konfigurację i ponawiaj smoke aż do sukcesu; następnie uruchom pełny przebieg.
4. Uruchom pełny job przez `orx exp run <node_id> --backend slurm --host <host>` (na `home`: sekcja `home`).
5. Po każdym zgłoszeniu — smoke i pełnego joba — postępuj według sekcji Po zgłoszeniu joba.
6. Po wznowieniu odczytaj nowe P2P, sprawdź `orx runs`, `orx logs <run_id>` oraz pliki w `remoteRoot/runs/<runId>/`. Zweryfikuj wymagane artefakty, ich kompletność i sensowność, a nie tylko status ani exit code.
7. Anulowanie runu własnego eksperymentu: `orx exp cancel <node_id>`. Procesu `orx supervise` nie ruszaj.

## Po zgłoszeniu joba

`orx exp wake <node_id>` budzi Cię, gdy run zakończy się sukcesem albo błędem; nie budzi po anulowaniu. Nie obudzi Cię jednak job, który utknął w kolejce, dlatego przed uśpieniem sprawdzasz jego start:

1. Na klastrze zostań w turze najwyżej ok. 10 min od zgłoszenia i sprawdzaj kolejkę jednym poleceniem trwającym do ok. 2 min, powtarzanym w razie potrzeby:

   ```sh
   for i in $(seq 4); do ssh <host> "squeue --me --start -n <slug> -h -o '%i %T %S'"; sleep 25; done
   ```

2. Status `RUNNING` albo `START TIME` w ciągu 24 h → `orx exp wake <node_id>` i koniec tury.
3. Po ok. 10 min brak `START TIME` albo start za ponad 24 h → `orx exp cancel <node_id>`, zgłoś ten sam commit na innym odpowiednim hoście i wróć do kroku 1. Kolejne joby kieruj na inne hosty, dopóki kolejka tego hosta znów nie pokaże `START TIME`.
4. Na `home` nie ma kolejki: od razu `orx exp wake <node_id>` i koniec tury.

Każdy nowy run wymaga ponownego `orx exp wake`. Nie używaj `orx exp wait`. Pętla `sleep` jest dozwolona tylko w kroku 1.

## Naprawa i weryfikacja

- Infrastrukturę, środowisko, host, zasoby, timelimit, moduły, venv, pakiety, ścieżki i oczywiste błędy techniczne bez wpływu na pytanie lub metryki popraw sam; commituj poprawki i dokumentuj je w raporcie wynikowym.
- Gdy błąd, brak danych albo proponowana poprawka może zmienić metodę, dane, metryki, pytanie lub design, uzgodnij dalsze działanie z laborantem przez P2P przed kontynuacją. Profesor nie uczestniczy w korespondencji implementacyjnej.
- Kod wyjścia jest tylko sygnałem pomocniczym: status `Done` ani exit code `0` samodzielnie nie potwierdzają poprawnego wyniku. Sprawdź logi, wymagane pliki i kompletność wyników.
- Po udanym smoke nie powtarzaj go przy kolejnych poprawkach; wznów pełny eksperyment od właściwego punktu lub checkpointu.
- Po poprawnym wykonaniu i weryfikacji technicznej przekaż laborantowi `RESULTS_READY` zgodnie z `roles/programmer.md`. Laborant i profesor odpowiadają za interpretację naukową.

## `job.sbatch`

`orx exp run` pobiera skrypty z commita eksperymentu. W korzeniu brancha `orx/<slug>` umieść i zacommituj przed runem:

- `run.sh` — setup środowiska i wznawialna komenda wejściowa z briefu; kończy się kodem różnym od 0 przy niepowodzeniu. Ten sam skrypt uruchamia Slurm (przez `job.sbatch`) i `home` (jako komenda runu węzła).
- `job.sbatch` — dyrektywy zasobów, log w katalogu runu, wywołanie `run.sh` w snapshotcie `repo` i zapis kodu wykonania do `exit_code`. Nie zmieniaj go na zero przy niepowodzeniu.

Log runu jest dowodem: komenda wejściowa wypisuje na stdout konfigurację, commit, seed, dataset, split, liczbę przykładów, trenowane parametry, checkpoint i końcowe metryki. Wynik zapisany wyłącznie w pliku, bez śladu w logu, nie jest wystarczającym dowodem.

```bash
#!/usr/bin/env bash
#SBATCH --job-name=<slug>
#SBATCH --output=log
#SBATCH --error=log
#SBATCH --partition=<partition>
#SBATCH --time=<minimum>
set +e
(
  cd repo || exit 97
  bash run.sh
)
code=$?
printf '%s\n' "$code" > exit_code
exit "$code"
```

Dodaj pozostałe dyrektywy zasobów potrzebne wybranemu hostowi, np. `--gres` lub `--mem`. Dla Heliosa użyj jego profilu logowania i konfiguracji ARM. Nie wpisuj zasobów na zapas.

## `home`

`home` nie ma Slurma. Uruchamiasz na nim przez `orx exp run <node_id> --backend ssh --host home`; backend ssh nie czyta `job.sbatch`, tylko wykonuje komendę runu węzła `cd repo 2>/dev/null; bash run.sh` (`experiments.md`), a log i kod wyjścia zapisuje sam w `~/.orx/runs/<runId>/` na `home`. Sprawdź komendę runu w `orx exp status <node_id>`; jeśli jest inna, zgłoś laborantowi i orchestratorowi `FLOW_BLOCKED`. Venv, dane i cache na `home` trzymaj w `~/research/<slug>/`.
