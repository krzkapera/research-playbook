# Układ repozytorium

Ten katalog zawiera instrukcje wspólne dla agentów. Właściwe repozytorium badawcze może być osobnym checkoutem; wtedy ten cały katalog należy skopiować lub udostępnić agentom jako kontekst projektu — jedną paczką, bo wszystko, czego agent może potrzebować (łącznie z brief badawczy), jest w środku.

```text
project/
  README.md
  access-matrix.md
  agent-start.md
  research-brief.md
  common/
    rules.md
    communication.md
    identifiers.md
  file-lifecycle.md
  model-assignment.md
  repository-layout.md
  worktrees.md
  hypotheses.md
  experiments.md
  roles/
    professor.md
    laborant.md
    professor-laborant.decision-maker.md
    programmer.md
    programmer.operator.md
    critic.md
    librarian.md
  literature/
    index.md
    artykuly/
    fsad/
```

`orx` dostarcza worktree każdej sesji automatycznie, we własnym katalogu danych — `project/` nie rezerwuje już na to osobnego miejsca (patrz `worktrees.md`).

Hipotezy i eksperymenty nie mają własnych plików ani katalogów w tym repozytorium — żyją jako węzły drzewa `orx` (patrz `hypotheses.md`, `experiments.md`).

## Gdzie zapisywać

- `literature/` — korpus PDF-ów literatury (`artykuly/`, `fsad/`) i jego spis (`index.md`), utrzymywany przez `librarian` (patrz `roles/librarian.md`); to jedyne miejsce w `project/`, które agent zapisuje, nie tylko czyta.
- synteza dotycząca konkretnej hipotezy albo eksperymentu trafia do `description` tego węzła w `orx`, nie do osobnego pliku (patrz `common/communication.md`).
- artefakty runów: patrz `file-lifecycle.md` — kod w branchu, duże/surowe dane na HPC (`~/scratch/<projekt>`), nic pośredniego w `project/`.

## Kanały i zadania

- `project` — wspólny stan i decyzje przekrojowe.
- kanał nazwany slugiem węzła — cała rozmowa dotycząca jednej hipotezy albo jednego eksperymentu (patrz `common/communication.md`).
- P2P — rozmowa ograniczona do wskazanych agentów.
- zadania `ai-crew-sync` — konkretna robota do zlecenia (implementacja, uruchomienie, analiza), z odwołaniem do sluga węzła w tytule.

## Co jest aktualnie aktywne

Nie ma osobnego indeksu do odczytania na start. `orx project view <project_id>` pokazuje całe drzewo hipotez i eksperymentów z bieżącym statusem; lista zadań `ai-crew-sync` pokazuje, co jest aktualnie zlecone i przez kogo. Jeśli któryś węzeł nie ma jasnego właściciela, agent nie zgaduje — pyta na kanale `project`.
