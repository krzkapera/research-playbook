# Start sesji agenta

Wykonaj te kroki przed pierwszą merytoryczną wiadomością:

1. Odczytaj `access-matrix.md`.
2. Odczytaj wyłącznie pliki z `common/` wymienione w macierzy oraz przekazany plik persony z `roles/` — a jeśli on odsyła dalej do plików domenowych (patrz `README.md`), przeczytaj też je.
3. Trzymaj się tego, do czego odsyła plik persony. Czytaj wyłącznie dokumenty wskazane tam i w `access-matrix.md`.
4. `agent_id` ustala `ai-crew-sync` automatycznie z tokena Twojej sesji — nie szukaj go. Ustal za to slug hipotezy i/albo eksperymentu, odbiorcę i oczekiwany rezultat: szukaj ich najpierw w bieżącym komunikacie, potem na kanale `project`, potem przez `orx project view <project_id>` — a gdy nieznany jest też `project_id`, przez `orx projects` (listuje wszystkie, bez potrzeby podawania żadnego id).
5. Jeśli któregoś pola nadal nie da się ustalić, zapytaj o nie krótko nadawcę.
6. Dołącz do kanałów wymienionych w briefie spawnu natychmiast (brief = zaproszenie), oraz do kanałów według `common/communication.md` (sekcja „Dołączanie do kanałów"):
   - zawsze do `project`;
   - do kanału sluga hipotezy/eksperymentu tylko wtedy, gdy masz w tym węźle aktywną rolę teraz (jesteś właścicielem etapu, zostałeś zaproszony do recenzji, albo zlecenie wskazuje ten slug i oczekuje Twojego udziału);
   - nie dołączaj do kanałów „na zapas";
   - samotne draftowanie u właściciela etapu nie wymaga zapraszania innych — zaproszenie = otwarcie rundy recenzji, nie start pracy solo.
7. Przeczytaj opis węzła na poziomie zlecenia: przy znanym slug/id hipotezy — węzeł hipotezy (`orx exp desc`/`orx exp status`); przy znanym slug/id eksperymentu — dopiero wtedy węzeł eksperymentu. Potem artefakty i logi (`orx logs`) wskazane w zleceniu. **Wyjątki:** (a) brief bez żadnego sluga/id — pomiń odczyt węzłów `orx`, dołącz do `project` i kanałów z briefu; (b) jest tylko hipoteza — czytaj węzeł hipotezy i pomiń węzeł eksperymentu.
8. Gdy potrzebujesz znaleźć innych agentów, wołaj `list_agents` (heartbeat nie jest wymagany). Potwierdź krótko, że jesteś gotowy: rola, cel, co i gdzie oddasz w wyniku. Swobodny, krótki tekst — bez wymuszonego formatu (patrz `common/communication.md`).

## Przekazany plik roli

macierz dostępu (`access-matrix.md`) ustala wspólne zasady i dozwolony zestaw lektur

Jeśli plik roli jest sprzeczny z dokumentem hipotezy, eksperymentu albo aktualną decyzją zespołu, zgłoś konflikt na właściwym kanale i czekaj na rozstrzygnięcie.

## Zakres odczytu

Czytaj pliki swojej roli i dokumenty z macierzy dostępu. Przeglądaj drzewo (`orx project view <project_id>`) oraz `literature/` tylko gdy zadanie albo persona tego wymagają; sięgaj po wskazany węzeł i wskazane artefakty.

Jeśli dokument, do którego odsyła zlecenie, nie znajduje się w Twoim zakresie, zapytaj o niego zamiast czytać go samodzielnie.

## Rozpoznawanie poziomu pracy

- Bez sluga: sprawa projektu lub nowa propozycja.
- Slug hipotezy, bez sluga eksperymentu: rozmowa o hipotezie.
- Slug hipotezy i sluga eksperymentu-dziecka: konkretny eksperyment.
- Identyfikator runu (`orx runs`): wykonanie jednego joba.
- Ścieżka artefaktu/logu: analiza konkretnego wyniku.

Jeżeli zadanie miesza poziomy, rozdziel odpowiedź i wskaż, co wymaga decyzji na poziomie hipotezy, a co jest tylko technicznym krokiem eksperymentu.
