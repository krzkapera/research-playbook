# Macierz dostępu do dokumentacji

Agent czyta wyłącznie:

1. `agent-start.md`
2. wspólne lektury w `~/playbook/` wymienione niżej (`communication.md`, `identifiers.md`)
3. przekazany plik roli z `roles/` oraz pliki, do których ten plik odsyła
4. bieżący węzeł hipotezy/eksperymentu w `orx` albo artefakt wskazany w zleceniu
5. `research-brief.md`, gdy rola każe go przeczytać albo gdy trzeba sprawdzić zakres badania

Komunikację, oczekiwanie i koniec tury prowadzisz według playbooka; skille harnessu (`orx-*`) stosujesz w pozostałym zakresie.

Dokument spoza tej listy, do którego odsyła zlecenie: pytasz o niego nadawcę zlecenia (`communication.md` § Roundtrip). Tokeny, konfiguracje i zmienne środowiskowe innych agentów są poza zakresem lektury (`agent-start.md`, krok 1).

## Wspólne lektury

- `communication.md`
- `identifiers.md`
- `agent-start.md`
- przekazany plik roli

## Role i dodatki

Tożsamości do adresowania i spawnu są role: `professor`, `laborant` i `programmer`. `programmer` jest rolą kodera i sam uruchamia, monitoruje i weryfikuje joby swojego eksperymentu (`roles/programmer.md` § Obsługa jobów). Rolę bierzesz wyłącznie z przekazanego pliku roli. Adres P2P sesji: `communication.md` § P2P.

## Źródło prawdy

Playbook (`~/playbook/`) jest read-only dla agentów; zmienia go wyłącznie użytkownik. Stan badań żyje w węzłach `orx` i na kanałach `ai-crew-sync`; edytuje go agent aktualnie odpowiedzialny za etap (`communication.md`).
