# Macierz dostępu do dokumentacji

Agent czyta wyłącznie:

1. `agent-start.md`
2. pliki z `common/` wymienione niżej
3. przekazany plik persony z `roles/` oraz pliki, do których ten plik odsyła
4. bieżący węzeł hipotezy/eksperymentu w `orx` albo artefakt wskazany w zleceniu
5. `research-brief.md`, gdy persona każe go przeczytać albo gdy trzeba sprawdzić zakres badania
6. `model-assignment.md`, gdy spawnujesz albo dobierasz harness/model

## Wspólne lektury

- `common/rules.md`
- `common/communication.md`
- `common/identifiers.md`
- `agent-start.md`
- przekazany plik persony

## Persony i dodatki

Jedynymi tożsamościami do adresowania i spawnu są persony: `professor`, `laborant`, `programmer`, `critic`, `librarian`. Rolę bierzesz wyłącznie z przekazanego pliku persony (może być kilka naraz).

Dodatki domenowe (nie są osobnymi personami):

- `roles/professor-laborant.decision-maker.md` — doklejany do professora (poziom hipotezy) albo laboranta (poziom eksperymentu)
- `roles/programmer.operator.md` — doklejany do programisty tylko gdy zadanie obejmuje HPC

Szablony briefów spawnu są w pliku persony, która spawnuje (nie w `common/`).

## Połączone persony

Gdy masz kilka przekazanych person naraz (np. okrojony skład z `model-assignment.md`), sumujesz lektury ze wszystkich tych plików i czytasz wyłącznie tę sumę. Jak kontynuować w jednej sesji zamiast spawnu „jako siebie” — `model-assignment.md`.

## Źródło prawdy

Dokumentacja `project/*.md`, `common/` i `roles/` jest read-only dla agentów — zmienia ją wyłącznie użytkownik. Stan badań żyje w węzłach `orx` i na kanałach `ai-crew-sync`; edytuje go agent aktualnie odpowiedzialny za etap (patrz `common/communication.md`). Wyjątek: `literature/index.md` dopisuje `librarian` pod lockiem (patrz `roles/librarian.md`).
