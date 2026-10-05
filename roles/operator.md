# Procedura obsługi jobów dla roli programmer

Ten plik zawiera procedury operacyjne stosowane przez programmera, który sam wykonuje eksperyment. Nie opisuje osobnej sesji ani przydziału agenta: programmer czyta go razem z `roles/programmer.md` i pozostaje właścicielem kodu, brancha oraz joba.

Wszystkie uruchomienia śledzone przez ORX wykonuj przez `job.sbatch` i `orx exp run` na backendzie Slurm. Nie uruchamiaj treningu bezpośrednio przez `sbatch` ani poza ORX.

## Pojęcia

- **Eksperyment** — węzeł wskazany w briefie kodera; brief podaje `project_id`, `node_id` i slug (`experiments.md`).
- **`description`** — źródło prawdy o pytaniu, uzgodnionym designie i ograniczeniach naukowych. Przed submitem czytaj jego aktualną treść.
- **`remoteRoot`** — katalog ORX określony w konfiguracji Slurm (domyślnie `~/scratch/.orx`): snapshoty w `source/`, runy w `runs/<runId>/`.
- **Smoke** — początkowy krótki test implementacji, przed pełnym uruchomieniem. Po udanym smoke nie powtarzaj go dla tego eksperymentu, także po poprawkach kodu lub wznowieniu.

## Wybór hosta i zasobów

1. Dobierz host do wymagań eksperymentu i aktualnej kolejki; uwzględnij dokumentację Cyfronetu oraz wzorce `.sh` i `.sbatch` z `~/scratch/`. Dla Heliosa uwzględnij ARM i jego właściwy profil.
2. Ares wybierz, gdy wystarcza CPU; nie kieruj tam jobów wymagających GPU. Athenę wybierz dla GPU, gdy jej zasoby wystarczą. Heliosa wybierz dla pełnych datasetów lub cięższych jobów. Uwzględnij też inne joby użytkownika.
3. Jeśli dla małego joba CPU rozważasz `home`, wyślij orchestratorowi P2P `HOME_ACCESS_REQUEST` (projekt, węzeł, krótki opis obciążenia i czasu) i zakończ turę. `home` wybierz dopiero po `HOME_ACCESS_ANSWER` z wyraźną zgodą użytkownika i potwierdzeniem, że komputer jest dostępny; sama dostępność SSH nie jest zgodą. Jeśli host nie jest poprawnie obsługiwany przez backend ORX/Slurm, zgłoś `FLOW_BLOCKED` orchestratorowi i laborantowi; nie obchodź ORX przez bezpośrednie SSH.
4. Przydziel minimalne wystarczające zasoby i timelimit. Sprawdź `squeue --start` na wybranym hoście. Jeśli po około 10 minutach od zgłoszenia brak `START TIME` albo planowany start jest za ponad 24 godziny, przenieś następne joby na inny odpowiedni host. Nie anuluj cudzych zadań.
5. Umieszczaj projekty, środowiska, cache, datasety, checkpointy, wyniki i logi na klastrze w `~/scratch/<nazwa-projektu>`; pliki ORX runu pozostają w `remoteRoot/runs/<runId>/`. Transferuj duże pliki bez pośredniego zapisywania na hoście agentów. Dane lokalnego `home`, jeśli użytkownik zatwierdził obliczenie, trzymaj w `~/research/<slug>/`.

## Przygotowanie i uruchomienie

1. Przed pierwszym jobem HPC przeczytaj dokumentację hosta oraz lokalne wzorce skryptów. Brief kodera i aktualny `description` określają wejścia, zależności, design, metryki oraz wymagane wyniki.
2. Przygotuj `job.sbatch` na branchu `orx/<slug>` i zacommituj go wraz z implementacją przed smoke/submitem. Job musi być wznawialny i zapisywać logi oraz wyniki w odrębnych lokalizacjach runu.
3. Wykonaj smoke na początku, tuż po przygotowaniu implementacji, przez `orx exp run`. Zapisz jego `run_id`. Po sukcesie nie powtarzaj smoke dla tego eksperymentu. Po błędzie ustal przyczynę, popraw implementację lub konfigurację i ponawiaj smoke aż do sukcesu; następnie uruchom pełny przebieg.
4. Uruchom pełny job przez `orx exp run <node_id> --backend slurm --host <host>`. Wszystkie runy śledzone przez ORX uruchamiaj w ten sposób.
5. Gdy job ma status `PENDING`, pozostań aktywny i monitoruj kolejkę; nie kończ tury ani nie używaj `orx exp wake`, dopóki nie potwierdzisz startu joba. Po potwierdzeniu startu zarejestruj oczekiwanie przez `orx exp wake <node_id>` i zakończ turę. Nie używaj `orx exp wait`.
6. Po wznowieniu odczytaj nowe P2P, sprawdź `orx runs`, `orx logs <run_id>` oraz pliki w `remoteRoot/runs/<runId>/`. Zweryfikuj wymagane artefakty, ich kompletność i sensowność, a nie tylko status ani exit code.
7. Anulowanie runu własnego eksperymentu: `orx exp cancel <node_id>`. Procesu `orx supervise` nie ruszaj.

## Naprawa i weryfikacja

- Infrastrukturę, środowisko, host, zasoby, timelimit, moduły, venv, pakiety, ścieżki i oczywiste błędy techniczne bez wpływu na pytanie lub metryki popraw sam; commituj poprawki i dokumentuj je w raporcie wynikowym.
- Gdy błąd, brak danych albo proponowana poprawka może zmienić metodę, dane, metryki, pytanie lub design, uzgodnij dalsze działanie z laborantem przez P2P przed kontynuacją. Profesor nie uczestniczy w korespondencji implementacyjnej.
- Kod wyjścia jest tylko sygnałem pomocniczym: status `Done` ani exit code `0` samodzielnie nie potwierdzają poprawnego wyniku. Sprawdź logi, wymagane pliki i kompletność wyników.
- Po udanym smoke nie powtarzaj go przy kolejnych poprawkach; wznów pełny eksperyment od właściwego punktu lub checkpointu.
- Po poprawnym wykonaniu i weryfikacji technicznej przekaż laborantowi `RESULTS_READY` zgodnie z `roles/programmer.md`. Laborant i profesor odpowiadają za interpretację naukową.

## `job.sbatch`

`orx exp run` pobiera skrypt z commita eksperymentu. Umieść `job.sbatch` na branchu `orx/<slug>` i zacommituj go przed runem. Skrypt ustawia minimalne wystarczające zasoby, zapisuje logi w katalogu runu, uruchamia komendę wejściową z briefu w snapshotcie `repo` i zapisuje kod wykonania do `exit_code`. Nie zmieniaj go na zero przy niepowodzeniu.

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
  <setup i wznawialna komenda wejściowa>
)
code=$?
printf '%s\n' "$code" > exit_code
exit "$code"
```

Dodaj pozostałe dyrektywy zasobów potrzebne wybranemu hostowi, np. `--gres` lub `--mem`. Dla Heliosa użyj jego profilu logowania i konfiguracji ARM. Nie wpisuj zasobów na zapas.
