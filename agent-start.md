# Start sesji agenta

Wykonaj te kroki przed pierwszą merytoryczną wiadomością:

1. Odczytaj `access-matrix.md`.
2. Odczytaj wyłącznie pliki z `common/` wymienione w macierzy oraz przekazany plik persony z `roles/` — a jeśli on odsyła dalej do plików domenowych (patrz `README.md`), przeczytaj też je.
3. Z macierzy wybierz zakres domenowy wynikający z przekazanej roli. Nie czytaj pozostałych dokumentów projektu.
4. Ustal `agent_id`, slug hipotezy i/albo eksperymentu, odbiorcę i oczekiwany rezultat. Szukaj ich najpierw w bieżącym komunikacie, potem na kanale `project`, potem przez `orx project view <project_id>`.
5. Jeśli któregoś pola nadal nie da się ustalić, nie zgaduj. Zapytaj o nie krótko nadawcę.
6. Dołącz do `project` oraz kanału nazwanego slugiem hipotezy, jeśli bieżąca praca jej dotyczy. Nie dołączaj do kanału eksperymentu, jeśli nie jest potrzebny.
7. Przeczytaj opis węzła hipotezy (`orx exp desc`/`orx exp status`), potem węzła eksperymentu, a następnie tylko artefakty i logi (`orx logs`) wskazane w zleceniu. Nie czytaj całego repozytorium bez potrzeby.
8. Potwierdź krótko, że jesteś gotowy: rola, cel, co i gdzie oddasz w wyniku. Swobodny, krótki tekst — bez wymuszonego formatu (patrz `common/communication.md`).

## Przekazany plik roli

Plik persony (`roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`) jest jedynym specjalnym wejściem przekazywanym agentowi przy uruchomieniu. Może wskazywać wprost, że masz kilka person naraz (patrz `model-assignment.md`). Agent wykonuje obowiązki opisane w tym pliku i w plikach domenowych, do których on odsyła, a macierz dostępu wskazuje pozostałe wspólne i domenowe dokumenty potrzebne do pracy.

Jeśli plik roli jest sprzeczny z dokumentem hipotezy, eksperymentu albo aktualną decyzją zespołu, zgłoś konflikt na właściwym kanale. Nie zmieniaj samodzielnie swojej roli.

## Zakres odczytu

Nie czytaj plików innych ról ani dokumentów spoza macierzy dostępu. Nie przeglądaj całego drzewa (`orx project view <project_id>`) ani całego `research-N/` "na wszelki wypadek"; czytaj tylko węzeł i artefakty wskazane w zadaniu.

Jeśli dokument, do którego odsyła zlecenie, nie znajduje się w Twoim zakresie, zapytaj o niego zamiast czytać go samodzielnie.

## Rozpoznawanie poziomu pracy

- Bez sluga: sprawa projektu lub nowa propozycja.
- Slug hipotezy, bez sluga eksperymentu: rozmowa o hipotezie.
- Slug hipotezy i sluga eksperymentu-dziecka: konkretny eksperyment.
- Identyfikator runu (`orx runs`): wykonanie jednego joba.
- Ścieżka artefaktu/logu: analiza konkretnego wyniku.

Jeżeli zadanie miesza poziomy, rozdziel odpowiedź i wskaż, co wymaga decyzji na poziomie hipotezy, a co jest tylko technicznym krokiem eksperymentu.
