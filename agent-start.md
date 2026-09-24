# Start sesji agenta

Pliki playbooka (`agent-start.md`, `access-matrix.md`, `communication.md`, `identifiers.md`, `model-assignment.md`, `hypotheses.md`, `experiments.md`, `research-brief.md`, `roles/…`) leżą w katalogu głównym worktree sesji, czyli w bieżącym katalogu roboczym. Czytasz je ścieżkami względnymi od tego katalogu.

## Kroki startu

Przed pierwszą merytoryczną wiadomością:

1. **Tożsamość na busie.** Wywołaj narzędzie MCP `whoami` (`ai-crew-sync`). Poprawny wynik: `agent` = nazwa agenta Twojego harnessu (tabela niżej) oraz `session` = wartość `$ORX_CHAT_SESSION_ID`. Gdy wynik jest inny albo MCP `ai-crew-sync` się nie ładuje lub zwraca błąd: nie publikujesz niczego, w odpowiedzi (do rodzica albo użytkownika) podajesz blokadę z dokładnym wynikiem i kończysz turę.
2. Odczytaj `access-matrix.md`, wspólne lektury (`communication.md`, `identifiers.md`) i przekazany plik roli z `roles/`. Gdy rola odsyła do pliku domenowego, przeczytaj też jego.
3. Ustal `project_id` według `identifiers.md`. Potem ustal slug i `id` węzła, odbiorcę i oczekiwany rezultat: najpierw z briefu; przy znanym `project_id` z `orx project view <project_id>` (drzewo węzłów: `id`, tytuł, branch).
4. Pole, którego nadal nie da się ustalić, doprecyzowujesz z nadawcą briefu (`communication.md` § Roundtrip).
5. Dołącz do kanałów z briefu (`communication.md` § Kanały) i potwierdź na każdym krótko: rola, cel, co i gdzie oddasz.
6. Przeczytaj węzeł z briefu: `orx exp desc <id>` i `orx exp status <id>` (hipoteza albo eksperyment, zgodnie z briefem), potem artefakty i logi wskazane w briefie. Brief bez węzła: pomiń ten krok.
7. Konflikt pliku roli z `description` węzła albo z decyzją właściciela etapu zgłaszasz wpisem na kanale węzła i czekasz na odpowiedź (`communication.md` § Czekanie).

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
- Sprawdzaj własne i cudze założenia.
- Hipoteza, eksperyment, implementacja, infrastruktura i interpretacja to rozdzielne poziomy; w jednej odpowiedzi oznaczasz poziom każdej części.
- Kolejny krok wynika z aktualnych dowodów; planuj jeden mały krok naprzód.

## Poziom pracy

- Bez sluga: sprawa projektu lub nowa propozycja.
- Slug hipotezy, bez sluga eksperymentu: rozmowa o hipotezie.
- Slug hipotezy i slug eksperymentu-dziecka: konkretny eksperyment.
- Identyfikator runu (`orx runs`): wykonanie jednego joba.
- Ścieżka artefaktu/logu: analiza konkretnego wyniku.

Gdy zadanie miesza poziomy, rozdziel odpowiedź: co jest decyzją na poziomie hipotezy, a co krokiem technicznym eksperymentu.
