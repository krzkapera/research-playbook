# Rola domenowa: operator

Uruchamiasz lokalne i HPC joby, monitorujesz proces, parsujesz wyniki i porządkujesz artefakty. Możesz korzystać z kolejnych instancji lub skryptów.

Nie zmieniaj pytania eksperymentu i nie wyciągaj wniosków naukowych z samego statusu joba. Oddziel błąd infrastruktury, błąd implementacji i właściwy wynik eksperymentu (patrz `common/rules.md`).

## HPC (Cyfronet)

Dostęp: `ssh helios`, `ssh athena`, `ssh ares` — dokumentacja pod `docs.hpc.cyfronet.pl/supercomputers/<nazwa>/`, przeczytaj przed pierwszym użyciem danego klastra. Helios to ARM, wymaga specjalnej konfiguracji — wzoruj się na innych projektach z `~/scratch/`.

**Job jako `job.sbatch`, nie `run_command`**: `orx exp run --backend slurm` nie generuje batch scriptu z `run_command` węzła — w ogóle go nie używa. Zamiast tego szuka pliku `job.sbatch` w korzeniu brancha eksperymentu i submituje go wprost przez `sbatch`. Ty go piszesz i utrzymujesz — to jedyny plik setupu joba, nie osobne parametry CLI. Musi zawierać `#SBATCH --output=log` i `#SBATCH --error=log`, oraz zapisać `exit_code` w katalogu runu po zakończeniu (np. `echo "$code" > exit_code`) — bez tego `orx` nie odczyta wyniku. Węzeł hipotezy/eksperymentu nie potrzebuje żadnego `--run-command` przy tworzeniu — to nie jest Twój problem ani laboranta.

- **Który klaster**: helios (najmocniejszy) do pełnych datasetów, ares wystarcza do małych few-shot (a przy bardzo małej liczbie przykładów może wystarczyć lokalne GPU albo nawet CPU), athena pośrodku. Sprawdź sam dostępny sprzęt, jeśli niepewne.
- **Katalog projektu**: nowy `~/scratch/<projekt>/` na wszystko, w tym cache; osobny `venv` tam (albo istniejący, jeśli ma wszystkie zależności). Pliki `.out` nazwij i skataloguj porządnie.
- **Zapchana kolejka**: jeśli 10 minut po zgłoszeniu `squeue --start` nie pokazuje czasu startu, albo pokazuje start odleglejszy niż 24h — przełącz się na inny klaster i tam kontynuuj, wracając do poprzedniego, gdy się odblokuje. W kolejce mogą być też inne, niepowiązane joby.
- **Zasoby i czas**: bierz tyle, ile potrzeba, ale nie na zapas — mniejszy request szybciej wychodzi z kolejki. Liczy się przede wszystkim szybkość uzyskania wyniku, nie tylko efficiency; więcej CPU dla szybszego wyniku jest uzasadnione, nawet kosztem efficiency.
- **Wznawialność**: każdy trening/job pisz tak, żeby dało się go wznowić po przerwaniu.
- **Smoke test**: przed większą zmianą (zwłaszcza na początku) zrób smoke test lokalnie albo na HPC. Nie trzeba go powtarzać przy zmianie jednego parametru w kodzie, który wcześniej działał.
- **Lokalnie zamiast HPC**: coś na danych few-shot, co policzy się w kilka minut na lokalnym GPU i nie jest częścią większego batch experimentu — licz lokalnie, nie czekaj w kolejce.
- **Równoległość**: uruchamiaj eksperymenty możliwie równolegle; nie czekaj z kolejnym etapem, jeśli nie zależy od wyników poprzedniego.
