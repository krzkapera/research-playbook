# Układ repozytorium

Ten katalog zawiera instrukcje wspólne dla agentów. Właściwe repozytorium badawcze może być osobnym checkoutem; wtedy te pliki należy skopiować lub udostępnić agentom jako kontekst projektu.

```text
project/
  README.md
  access-matrix.md
  agent-start.md
  common/
    rules.md
    communication.md
    identifiers.md
  coordination-flow.md
  file-lifecycle.md
  model-assignment.md
  repository-layout.md
  worktrees.md
  hypotheses.md
  experiments.md
  roles/
    common.md
    decision-maker.md
    researcher.md
    critic.md
    experiment-designer.md
    implementer.md
    operator.md
    analyst.md
    librarian.md
  templates/
    hypothesis.md
    experiment.md
  research-N/
    papers/
    notes/
    synthesis/
```

`orx` dostarcza worktree każdej sesji automatycznie, we własnym katalogu danych — `project/` nie rezerwuje już na to osobnego miejsca (patrz `worktrees.md`).

Hipotezy i eksperymenty nie mają własnych plików ani katalogów w tym repozytorium — żyją jako węzły drzewa `orx` (patrz `hypotheses.md`, `experiments.md`). `templates/` opisuje treść, jaką wpisujesz do `description` takiego węzła, nie plik do utworzenia.

## Gdzie zapisywać

- `research-N/` — literatura i synteza researchu zgodnie z `project.txt`; nadal pliki, bo pojedyncza praca/PDF nie mieści się w limicie note'a `ai-crew-sync` (1 MiB) i nie jest tym, co `orx` śledzi.
- artefakty runów: patrz `file-lifecycle.md` — kod w branchu, duże/surowe dane na HPC (`~/scratch/<projekt>`), nic pośredniego w `project/`.

## Kanały i zadania

- `project` — wspólny stan i decyzje przekrojowe.
- kanał nazwany slugiem węzła — cała rozmowa dotycząca jednej hipotezy albo jednego eksperymentu (patrz `common/communication.md`).
- P2P — rozmowa ograniczona do wskazanych agentów.
- zadania `ai-crew-sync` — konkretna robota do zlecenia (implementacja, uruchomienie, analiza), z odwołaniem do sluga węzła w tytule.

## Co jest aktualnie aktywne

Nie ma osobnego indeksu do odczytania na start. `orx project view <project_id>` pokazuje całe drzewo hipotez i eksperymentów z bieżącym statusem; lista zadań `ai-crew-sync` pokazuje, co jest aktualnie zlecone i przez kogo. Jeśli któryś węzeł nie ma jasnego właściciela, agent nie zgaduje — pyta na kanale `project`.
