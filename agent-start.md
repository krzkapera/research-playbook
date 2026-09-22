# Start sesji agenta

Wykonaj te kroki przed pierwszą merytoryczną wiadomością:

1. Odczytaj `access-matrix.md`.
2. Odczytaj wyłącznie pliki z `common/` wymienione w macierzy oraz przekazany plik persony z `roles/` — a jeśli on odsyła dalej do plików domenowych (patrz `README.md`), przeczytaj też je.
3. Trzymaj się tego, do czego odsyła plik persony. Nie czytaj pozostałych dokumentów projektu.
4. `agent_id` ustala `ai-crew-sync` automatycznie z tokena Twojej sesji — nie szukaj go. Ustal za to slug hipotezy i/albo eksperymentu, odbiorcę i oczekiwany rezultat: szukaj ich najpierw w bieżącym komunikacie, potem na kanale `project`, potem przez `orx project view <project_id>` — a gdy nieznany jest też `project_id`, przez `orx projects` (listuje wszystkie, bez potrzeby podawania żadnego id).
5. Jeśli któregoś pola nadal nie da się ustalić, nie zgaduj. Zapytaj o nie krótko nadawcę.
6. Dołącz do kanałów wymienionych w briefie spawnu natychmiast (brief = zaproszenie), oraz do kanałów według `common/communication.md` (sekcja „Dołączanie do kanałów"):
   - zawsze do `project`;
   - do kanału sluga hipotezy/eksperymentu tylko wtedy, gdy masz w tym węźle aktywną rolę teraz (jesteś właścicielem etapu, zostałeś zaproszony do recenzji, albo zlecenie wskazuje ten slug i oczekuje Twojego udziału);
   - nie dołączaj do kanałów „na zapas";
   - samotne draftowanie u właściciela etapu nie wymaga zapraszania innych — zaproszenie = otwarcie rundy recenzji, nie start pracy solo.
7. Przeczytaj opis węzła hipotezy (`orx exp desc`/`orx exp status`), potem węzła eksperymentu, a następnie tylko artefakty i logi (`orx logs`) wskazane w zleceniu. Nie czytaj całego repozytorium bez potrzeby. **Wyjątek:** gdy brief spawnu nie wskazuje sluga/id węzła, pomiń odczyt węzłów `orx` i nie zgaduj slugów — dołącz tylko do `project` oraz kanałów z briefu.
8. Gdy potrzebujesz znaleźć innych agentów, wołaj `list_agents` (heartbeat nie jest wymagany). Potwierdź krótko, że jesteś gotowy: rola, cel, co i gdzie oddasz w wyniku. Swobodny, krótki tekst — bez wymuszonego formatu (patrz `common/communication.md`).

## Przekazany plik roli

Plik persony (`roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`) jest jedynym specjalnym wejściem przekazywanym agentowi przy uruchomieniu. Może wskazywać wprost, że masz kilka person naraz (patrz `model-assignment.md`). Agent wykonuje obowiązki opisane w tym pliku i w plikach domenowych, do których on odsyła — macierz dostępu (`access-matrix.md`) ustala tylko wspólne dla wszystkich zasady i granicę tego, czego nie wolno czytać poza tym.

Jeśli plik roli jest sprzeczny z dokumentem hipotezy, eksperymentu albo aktualną decyzją zespołu, zgłoś konflikt na właściwym kanale. Nie zmieniaj samodzielnie swojej roli.

## Zakres odczytu

Nie czytaj plików innych ról ani dokumentów spoza macierzy dostępu. Nie przeglądaj całego drzewa (`orx project view <project_id>`) ani całego `literature/` "na wszelki wypadek"; czytaj tylko węzeł i artefakty wskazane w zadaniu.

Jeśli dokument, do którego odsyła zlecenie, nie znajduje się w Twoim zakresie, zapytaj o niego zamiast czytać go samodzielnie.

## Rozpoznawanie poziomu pracy

- Bez sluga: sprawa projektu lub nowa propozycja.
- Slug hipotezy, bez sluga eksperymentu: rozmowa o hipotezie.
- Slug hipotezy i sluga eksperymentu-dziecka: konkretny eksperyment.
- Identyfikator runu (`orx runs`): wykonanie jednego joba.
- Ścieżka artefaktu/logu: analiza konkretnego wyniku.

Jeżeli zadanie miesza poziomy, rozdziel odpowiedź i wskaż, co wymaga decyzji na poziomie hipotezy, a co jest tylko technicznym krokiem eksperymentu.
