# Dodatek: operator HPC

Warunkowy dodatek do `programmer` (joby HPC / kolejka / przełączanie klastra) — nie osobna rola.

Status joba = infrastruktura. W raporcie oddziel: błąd infrastruktury, błąd implementacji, wynik naukowy (`common/rules.md`). Pytanie eksperymentu bierz z `description`.

Klastry, scratch, kolejka, smoke, kiedy liczyć lokalnie → `research-brief.md` (HPC). Tu tylko to, czego brief nie precyzuje dla `orx`:

## job.sbatch

`orx exp run --backend slurm` **nie** używa `run_command` węzła. Submituje `job.sbatch` z korzenia brancha eksperymentu. Ty ten plik piszesz i utrzymujesz.

W `job.sbatch` muszą być m.in. `#SBATCH --output=...`, `#SBATCH --error=...` oraz zapis `exit_code` w katalogu runu po zakończeniu (np. `echo "$code" > exit_code`) — bez tego `orx` nie odczyta wyniku. Przy tworzeniu węzła nie ustawia się `--run-command`.

Job wznawialny. Monitorowanie: `orx exp wait` / `orx exp wake` (nie własna pętla), poza przypadkami z briefu (np. przełączenie klastra przy martwej kolejce).

## Po jobie

Na kanale eksperymentu i w odpowiedzi spawnu: run id, ścieżki logów, status Done/Failed/Cancelled. Laborant wciąga to do `description`.
