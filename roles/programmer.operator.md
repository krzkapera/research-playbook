# Dodatek: operator HPC

Warunkowy dodatek do `programmer` (joby HPC / kolejka / przełączanie klastra) — nie osobna rola.

Status joba = infrastruktura. W raporcie oddziel: błąd infrastruktury, błąd implementacji, wynik naukowy (`experiments.md`). Pytanie eksperymentu bierz z `description`.

## Klastry i kolejka

Dostęp: `ssh helios`, `ssh athena`, `ssh ares` (dokumentacja Cyfronet: Helios/GPU, Athena, Ares). Helios = ARM — specjalna konfiguracja; wzoruj się na innych projektach w `~/scratch/`.

- Artefakty jobów: `~/scratch/<katalog-projektu>/` (kod, cache, venv, logi — porządek).
- Helios: najmocniejszy (pełne datasety). Ares: małe few-shot. Athena: środek. Bez GPU, gdy wystarczy CPU (Ares).
- Nie zapychaj kolejki. Sygnał problemu: po ~10 min od submitu `squeue --start` bez START TIME, albo START TIME > 24 h — przenieś pracę na inny klaster, wróć gdy kolejka odżyje.
- Job wznawialny; zasoby/timelimit: minimum do wyniku. Efficiency z hpc-jobs sensowna; więcej CPU OK, gdy skraca wall-clock.
- Przed większą zmianą / pierwszym jobem: lokalny smoke. Przy zmianie jednego sprawdzonego parametru wystarczy poprzedni smoke.
- Few-shot liczące się w kilka minut na lokalnym GPU → lokalnie (venv w katalogu projektu), nie kolejka.
- Czekanie na koniec runów: skrypty bash. Monitorowanie przez `orx` / ten dodatek.

## job.sbatch

`orx exp run --backend slurm` **nie** używa `run_command` węzła. Submituje `job.sbatch` z korzenia brancha eksperymentu. Ty ten plik piszesz i utrzymujesz.

W `job.sbatch` muszą być m.in. `#SBATCH --output=...`, `#SBATCH --error=...` oraz zapis `exit_code` w katalogu runu po zakończeniu (np. `echo "$code" > exit_code`) — bez tego `orx` nie odczyta wyniku. Przy tworzeniu węzła nie ustawia się `--run-command`.

Job wznawialny. Monitorowanie: `orx exp wait` / `orx exp wake` (nie własna pętla), poza przypadkami z briefu (np. przełączenie klastra przy martwej kolejce).

## Po jobie

Na kanale eksperymentu i w odpowiedzi spawnu: run id, ścieżki logów, status Done/Failed/Cancelled. Laborant wciąga to do `description`.
