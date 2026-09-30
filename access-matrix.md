# Macierz dostępu do dokumentacji

Agent czyta wyłącznie:

1. `agent-start.md`
2. wspólne lektury w `~/playbook/` wymienione niżej (`communication.md`, `identifiers.md`)
3. przekazany plik roli z `roles/` oraz pliki, do których ten plik odsyła
4. bieżący węzeł hipotezy/eksperymentu w `orx` albo artefakt wskazany w zleceniu
5. `research-brief.md`, gdy rola każe go przeczytać albo gdy trzeba sprawdzić zakres badania
6. `model-assignment.md`, gdy spawnujesz (komenda z tabeli dla roli)

Spawn, brief, czekanie i koniec tury prowadzisz według playbooka; skille harnessu (`orx-*`) stosujesz w pozostałym zakresie.

Dokument spoza tej listy, do którego odsyła zlecenie: pytasz o niego nadawcę zlecenia (`communication.md` § Roundtrip). Tokeny, konfiguracje i zmienne środowiskowe innych agentów są poza zakresem lektury (`agent-start.md`, krok 1).

## Wspólne lektury

- `communication.md`
- `identifiers.md`
- `agent-start.md`
- przekazany plik roli

## Role i dodatki

Jedynymi tożsamościami do adresowania i spawnu są role: `professor`, `laborant`, `programmer`, `operator`, `critic`, `librarian`. Rolę bierzesz wyłącznie z przekazanego pliku roli (może być kilka naraz). Adres P2P sesji: `communication.md` § P2P.

Szablony briefów spawnu: w pliku roli, która spawnuje.

## Połączone role

Gdy masz kilka przekazanych ról naraz (np. okrojony skład z `model-assignment.md`), sumujesz lektury ze wszystkich tych plików i czytasz wyłącznie tę sumę. Prowadzenie połączonych ról w jednej sesji: `model-assignment.md`.

## Źródło prawdy

Playbook (`~/playbook/`) jest read-only dla agentów; zmienia ją wyłącznie użytkownik. Stan badań żyje w węzłach `orx` i na kanałach `ai-crew-sync`; edytuje go agent aktualnie odpowiedzialny za etap (`communication.md`). Wyjątek: `~/literature/` zapisuje `librarian` (`identifiers.md` § Miejsca zapisu, `roles/librarian.md`).
