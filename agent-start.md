# Start sesji agenta

Pliki playbooka (`agent-start.md`, `access-matrix.md`, `communication.md`, `identifiers.md`, `hypotheses.md`, `experiments.md`, `research-brief.md`, `roles/…`) leżą w `~/playbook/`, poza repo projektu i worktree sesji. Ścieżki plików playbooka w tych dokumentach są względne wobec `~/playbook/`: `roles/operator.md` to `~/playbook/roles/operator.md` (procedura obsługi jobów używana przez programmera).

## Wersja playbooka

Obowiązuje wersja przeczytana na starcie sesji. Po wiadomości użytkownika „playbook zaktualizowany” czytasz ponownie z `~/playbook/` `agent-start.md`, `communication.md`, `identifiers.md` i swój plik roli; od tej chwili obowiązuje nowa wersja (`communication.md` § Wznowienie).

## Kroki startu

Przed pierwszą merytoryczną wiadomością:

1. **Tożsamość na busie.** Wywołaj narzędzie MCP `whoami` (`ai-crew-sync`). Poprawny wynik: `agent` = nazwa agenta Twojego harnessu (tabela niżej) oraz `session` = wartość `$ORX_CHAT_SESSION_ID`. Gdy wynik jest inny albo MCP `ai-crew-sync` się nie ładuje lub zwraca błąd: nie publikujesz niczego, w odpowiedzi (do rodzica albo użytkownika) podajesz blokadę z dokładnym wynikiem i kończysz turę.
2. Odczytaj `access-matrix.md`, wspólne lektury (`communication.md`, `identifiers.md`) i przekazany plik roli z `roles/`. Gdy rola odsyła do pliku domenowego, przeczytaj też jego.
3. Ustal `project_id` według `identifiers.md`. Potem ustal slug i `id` węzła, odbiorcę i oczekiwany rezultat: najpierw z briefu; przy znanym `project_id` z `orx project view <project_id>` (drzewo węzłów: `id`, tytuł, branch).
4. Pole, którego nadal nie da się ustalić, doprecyzowujesz z nadawcą briefu (`communication.md` § Roundtrip).
5. Postępuj zgodnie z instrukcjami swojej roli dotyczącymi kanałów i potwierdzenia udziału. Jeśli rodzic lub orchestrator ma odebrać rejestrację albo oddanie po zakończeniu swojej tury, wyślij mu także P2P.
6. Przeczytaj węzeł z briefu: `orx exp desc <id>` i `orx exp status <id>` (hipoteza albo eksperyment, zgodnie z briefem), potem artefakty i logi wskazane w briefie. Brief bez węzła: pomiń ten krok.
7. Konflikt pliku roli z `description` węzła albo z decyzją właściciela etapu zgłaszasz właścicielowi P2P, a jeśli dotyczy treści węzła, dodajesz też wpis na kanale. Gdy nie masz innej pracy, kończysz turę i wracasz po odpowiedzi (`communication.md` § P2P).

| Harness | `agent` w wyniku `whoami` |
|---|---|
| codex | `codex` |
| claude-code | `claude` |
| cursor | `cursor` |
| antigravity | `agy` |
| opencode | `opencode` |

Z busem łączysz się wyłącznie narzędziami MCP `ai-crew-sync` własnej sesji. Tokenów nie szukasz, konfiguracji ani zmiennych środowiskowych innych agentów nie czytasz, `ai-crew-sync client` ani cudzego tokenu nie używasz.

## Zasady pracy

- Kod jest narzędziem do badania, a nie celem samym w sobie.
- Każde zlecenie najpierw krytycznie sprawdź i przyjmij dopiero po wyjaśnieniu wątpliwości (sekcja Przyjęcie zlecenia).
- Hipoteza, eksperyment, implementacja, infrastruktura i interpretacja to rozdzielne poziomy; w jednej odpowiedzi oznaczasz poziom każdej części.
- Kolejny krok wynika z aktualnych dowodów; planuj jeden mały krok naprzód.

## Przyjęcie zlecenia

Zanim przyjmiesz zlecenie (prompt spawnu, brief, `IMPLEMENTATION_REQUEST`, `REQUEST_AGENT`, `NEXT_TEST` i inne), sprawdź je krytycznie względem briefu użytkownika, pliku roli, `description` węzła i dostępnych dowodów:

1. Ustal oczekiwany rezultat, zakres, odbiorcę i sposób oddania. Sprawdź, czy zlecenie wskazuje właściwy projekt, węzeł i potrzebne materiały.
2. Sprawdź, czy cel i proponowane działanie są spójne, wykonalne w Twojej roli i zgodne z nadrzędnymi instrukcjami. Wypisz brakujące szczegóły, sprzeczne wymagania, niepoparte założenia i błędne przesłanki, które mogą zmienić wynik.
3. Kwestie merytoryczne rozstrzygnij z właścicielem decyzji, a zakres i sposób oddania — z nadawcą zlecenia, przez P2P (`communication.md` § Roundtrip). Pytaj konkretnie: niejasność, jej wpływ na realizację i potrzebna decyzja. Do odpowiedzi zlecenie pozostaje nieprzyjęte; jeśli nie masz innej pracy, zakończ turę.
4. Gdy szczegóły są uzgodnione, przyjmij zlecenie i realizuj je. Jasne zlecenie przyjmujesz bez dodatkowej rundy pytań i bez osobnego potwierdzenia (`communication.md` § P2P).
5. Nieścisłość, która ujawni się później, zgłoś, zanim podejmiesz działanie zależne od nierozstrzygniętej decyzji.

Przyjęcie zlecenia dotyczy jego treści i zakresu, nie dostępności zasobów ani startu joba; blokady operacyjne zgłaszasz osobno według pliku roli.

## Poziom pracy

- **Bez węzła:** sprawa całego projektu albo propozycja nowej hipotezy, zanim powstanie jej węzeł.
- **Węzeł hipotezy (`id`, slug):** twierdzenie, zakres, stan i decyzje hipotezy; także jej eksperyment główny, jeśli jest prowadzony na tym węźle (`experiments.md`).
- **Węzeł eksperymentu-dziecka (`id`, slug) i jego hipoteza-rodzic:** konkretny dodatkowy test; hipoteza wyznacza twierdzenie, a eksperyment — pytanie i protokół.
- **`run_id`:** jedno wykonanie joba; zawsze ustal i podaj też węzeł, którego dotyczy.
- **Ścieżka artefaktu lub logu:** analiza wskazanego pliku. Jego węzeł i run ustal z briefu, `description` albo `orx runs`; sama ścieżka nie określa poziomu.

W rozmowie slug wystarcza do wskazania zakresu; w komendach `orx` używaj `id` węzła, a dla konkretnego uruchomienia `run_id` (`identifiers.md`).

Gdy zadanie miesza poziomy, rozdziel odpowiedź: decyzja na poziomie hipotezy, protokół eksperymentu, krok techniczny albo analiza pojedynczego runu.
