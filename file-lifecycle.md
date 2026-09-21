# Lifecycle plików i odpowiedzialność

## Zasada główna

Nie ma już współdzielonego pliku indeksu do chronienia, więc nie ma bespoke locka workflow. Wyłączność kodu i brancha zapewnia git/`orx` — jeden worktree na sesję, dostarczany automatycznie (patrz `worktrees.md`); wyłączność konkretnej roboty zapewnia claim zadania w `ai-crew-sync`. Każdy agent może zgłosić propozycję i krytykę; nie każdy może edytować dany węzeł.

## Dwa rodzaje plików

### Dokumentacja konfiguracji agentów — read-only

To są wszystkie dokumenty opisujące role, dostęp, komunikację, procedury i strukturę systemu:

```text
project/*.md
common/
roles/
```

Obejmuje to `hypotheses.md` i `experiments.md`, które opisują procedury, a nie konkretne obiekty badawcze. Agent ich nie edytuje. Są dostarczonym kontekstem pracy — zmienia je wyłącznie użytkownik.

### Stan badań — w orx i ai-crew-sync, nie w plikach `project/`

Hipotezy i eksperymenty to węzły drzewa `orx` (patrz `hypotheses.md`, `experiments.md`), nie pliki w tym repozytorium. Ich treść edytuje się przez `orx exp desc`; dyskusję i zlecenia prowadzi się przez kanały i zadania `ai-crew-sync` (patrz `common/communication.md`). `project/` nie przechowuje kopii tej treści.

### Korpus literatury — jedyny wyjątek od read-only

`literature/` (PDF-y i ich spis `index.md`) nie jest dokumentacją konfiguracji ani stanem badań — to współdzielony korpus, który utrzymuje `librarian`, dopisując do `index.md` pod lockiem `ai-crew-sync` (patrz `roles/librarian.md`). Żadna inna rola go nie edytuje.

## Kto edytuje węzeł

Węzeł hipotezy edytuje agent aktualnie pełniący za niego odpowiedzialność (professor/decision-maker na poziomie hipotezy, laborant/experiment-designer na poziomie eksperymentu) — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz. Inni agenci zgłaszają uwagi na kanale węzła; nie edytują go, dopóki właściciel jawnie nie przekaże im odpowiedzialności.

## Tworzenie hipotezy

1. Agent proponujący hipotezę tworzy węzeł-korzeń: `orx create-experiment <project_id> --title "..."` (bez `--parent`).
2. Zapisuje wstępną treść przez `orx exp desc --stdin` (patrz `hypotheses.md`).
3. Tworzy kanał `ai-crew-sync` o nazwie równej slugowi węzła.
4. Ogłasza powstanie na kanale `project`.

Utworzenie węzła nie oznacza przyjęcia hipotezy — opis od razu mówi wprost, że to dopiero propozycja.

## Tworzenie eksperymentu

1. Po decyzji o hipotezie, agent w roli experiment-designer/laborant tworzy węzeł-dziecko: `orx create-experiment <project_id> --parent <id-hipotezy> --title "..."` (`id` hipotezy, nie jej slug — patrz `common/identifiers.md`).
2. Zapisuje treść przez `orx exp desc --stdin` (patrz `experiments.md`).
3. Ogłasza na kanale hipotezy.

## Propozycje i krytyka

Krytyka i propozycje zmian zostają w kanale, jeśli nie zmieniają stanu wiedzy (patrz `common/communication.md`, "Zadanie czy dyskusja"). Gdy mają znaczenie dla dalszej interpretacji, właściciel węzła włącza je do jego `description` przy najbliższej aktualizacji. Nie ma osobnego katalogu na propozycje — historia kanału w `ai-crew-sync` jest wystarczającym trwałym zapisem.

## Artefakty

Kod i małe pliki istotne dla wniosku (figury, krótkie podsumowania) trafiają do brancha eksperymentu. Surowe, duże dane (checkpointy, pełne logi, datasety) zostają tam, gdzie faktycznie powstały — katalog projektu na HPC (`~/scratch/<projekt>`, patrz `worktrees.md`) — i są tylko wskazane ścieżką w `description`, nie kopiowane do repo. Wynik uruchomienia zawsze trafia też na stdout, żeby `orx logs <run-id>` był samodzielnym dowodem, niezależnie od plików.
