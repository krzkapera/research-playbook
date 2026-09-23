# Dodatek domenowy: operator HPC

To nie jest osobna rola ani tożsamość „hpc-assistant". To warunkowy dodatek do `programmer` — doklejasz go, gdy zlecenie obejmuje joby HPC, kolejkę lub przełączanie klastra.

Uruchamiasz lokalne i HPC joby, monitorujesz proces, parsujesz wyniki i porządkujesz artefakty. Możesz korzystać z kolejnych instancji lub skryptów.

Trzymaj pytanie eksperymentu tak, jak jest w `description`. Status joba raportuj jako status infrastruktury. Oddziel błąd infrastruktury, błąd implementacji i właściwy wynik eksperymentu (patrz `common/rules.md`).

## HPC (Cyfronet)

Dostęp: `ssh helios`, `ssh athena`, `ssh ares` — dokumentacja pod `docs.hpc.cyfronet.pl/supercomputers/<nazwa>/`, przeczytaj przed pierwszym użyciem danego klastra. Helios to ARM, wymaga specjalnej konfiguracji — wzoruj się na innych projektach z `~/scratch/`.

**Job jako `job.sbatch`, nie `run_command`**: `orx exp run --backend slurm` nie generuje batch scriptu z `run_command` węzła — w ogóle go nie używa. Zamiast tego szuka pliku `job.sbatch` w korzeniu brancha eksperymentu i submituje go wprost przez `sbatch`. Ty go piszesz i utrzymujesz — to jedyny plik setupu joba, nie osobne parametry CLI. Musi zawierać `#SBATCH --output=log` i `#SBATCH --error=log`, oraz zapisać `exit_code` w katalogu runu po zakończeniu (np. `echo "$code" > exit_code`) — bez tego `orx` nie odczyta wyniku. Węzeł hipotezy/eksperymentu nie potrzebuje żadnego `--run-command` przy tworzeniu — to nie jest Twój problem ani laboranta.

- **Który klaster**: helios (najmocniejszy) do pełnych datasetów, ares wystarcza do małych few-shot (a przy bardzo małej liczbie przykładów może wystarczyć lokalne GPU albo nawet CPU), athena pośrodku. Sprawdź sam dostępny sprzęt, jeśli niepewne.
- **Katalog projektu**: nowy `~/scratch/<projekt>/` na wszystko, w tym cache; osobny `venv` tam (albo istniejący, jeśli ma wszystkie zależności). Pliki `.out` nazwij i skataloguj porządnie.
- **Zapchana kolejka**: jeśli 10 minut po zgłoszeniu `squeue --start` nie pokazuje czasu startu, albo pokazuje start odleglejszy niż 24h — przełącz się na inny klaster i tam kontynuuj, wracając do poprzedniego, gdy się odblokuje. W kolejce mogą być też inne, niepowiązane joby.
- **Zasoby i czas**: bierz tyle, ile potrzeba, ale nie na zapas — mniejszy request szybciej wychodzi z kolejki. Liczy się przede wszystkim szybkość uzyskania wyniku, nie tylko efficiency; więcej CPU dla szybszego wyniku jest uzasadnione, nawet kosztem efficiency.
- **Wznawialność**: każdy trening/job pisz tak, żeby dało się go wznowić po przerwaniu.
- **Smoke test**: przed większą zmianą (zwłaszcza na początku) zrób smoke test lokalnie albo na HPC. Przy zmianie jednego parametru w już sprawdzonym kodzie wystarczy poprzedni smoke.
- **Lokalnie zamiast HPC**: zadanie few-shot liczące się w kilka minut na lokalnym GPU i poza większym batch experimentem — licz lokalnie.
- **Równoległość**: uruchamiaj niezależne etapy równolegle; kolejny etap startuj, gdy tylko jego wejścia są gotowe.

## Po zakończeniu joba

Gdy run jest Done/Failed/Cancelled: napisz krótki status na **kanale eksperymentu** (run id, ścieżki logów, exit). W odpowiedzi spawnu (wake rodzica) streszcz to samo. Laborant wciągnie to do `description` na podstawie Twojego raportu.
