# Wspólna komunikacja

Narzędzie: `ai-crew-sync` — wiadomości, P2P (`ask_agent`), kanały, zadania, locki, `wait_for_updates`. Stan węzła żyje w `description` (`orx`).

## Kanały

- Zawsze dołączaj do `project`.
- Do kanału sluga węzła dołączaj tylko przy aktywnej roli *teraz* (właściciel etapu, zaproszenie do recenzji, albo zlecenie wskazuje ten slug).
- Bez dołączania na zapas.
- Draft solo nie wymaga innych na kanale. Wciąganie ludzi na kanał = start rundy recenzji.

Kanał o nazwie sluga powstaje, gdy twórca węzła dołączy i napisze pierwszą wiadomość. Zaproszenie = podanie nazwy kanału w briefie albo P2P.

## Opis węzła vs wiadomość

`description` jest źródłem prawdy o stanie i decyzjach — aktualizuj na bieżąco. Wiadomość na kanale to krótka delta: co się zmieniło i o co chodzi. Historia kanału = archiwum „dlaczego”.

`description` edytuje wyłącznie właściciel etapu: `professor` (hipoteza), `laborant` (eksperyment). Inne role oddają materiał na kanale lub w odpowiedzi spawnu; właściciel wciąga go do `description`.

## Pokój (recenzja)

Pokój = recenzja **gotowego** draftu z `description`. Skład rośnie stopniowo (kolejna osoba → kolejna runda). Właściciel etapu ma głos rozstrzygający przy braku zgody. Pokój kończy się, gdy wracasz do solo albo zmieniasz etap.

## Czekanie

Czekaj przez `wait_for_updates` z `channel: "<slug>"`. Spawn zawsze z `--no-wake`.

## Zadanie vs dyskusja

- Robota z postępem węzła → zadanie `ai-crew-sync` (`create_task` / `claim_task`), może mieć właściciela i zależności.
- Krytyka, pytania, propozycje → zwykła dyskusja na kanale (bez claim).

`create_task`, gdy pulę może wziąć którykolwiek z równoważnych aktywnych agentów (`list_agents`). Spawn dedykowanego helpera: brief spawnu wystarcza za zadanie.

## Spawn

- Przy każdym `orx agent spawn` zawsze `--no-wake`.
- Równoległe spawny OK; koordynacja na kanałach.

1. Sprawdź `list_agents` — gdy potrzebna rola już działa w tym kontekście, użyj `ask_agent`. Wyjątki: plik roli, która spawnuje.
2. Przy każdym `orx agent spawn` zawsze podaj `--no-wake`. Szczegóły flagi: skill `/orx-agent-delegation`.
3. Brief: kanały do natychmiastowego dołączenia, rola wprost, zadanie, oczekiwany wynik. `--harness` i `--model` wyłącznie wg `model-assignment.md`. Szablony briefów są w `roles/`.
4. Koordynacja równoległych dzieci = kanały (hipoteza / eksperyment). Kanał jest źródłem prawdy o trwającej pracy.
5. Kończąc: opcjonalna krótka odpowiedź spawnu do rodzica ≤ ~4000 znaków (status, skrót + odesłanie). Dłuższy materiał (ścieżki, logi, tabele) na uzgodnionym kanale. Bieżący stan: kanał i `description`.

Szablon briefu:

```text
Jesteś <rola> dla projektu <project_id>. Przeczytaj `roles/<plik-roli>.md` i kieruj się nim.

Slug hipotezy/eksperymentu: <slug, jeśli dotyczy>
Kanały dołącz natychmiast: project[, <slug>]
Zadanie: <konkretne, samodzielne>
Oczekiwany wynik: <co i w jakiej formie>
```

## Roundtrip (niejasny brief)

Gdy brief/`description` jest zbyt niejasny, by kontynuować — nie zgaduj. Doprecyzowanie idzie przez kanał w **tej samej** sesji dziecka:

1. Dziecko publikuje pytania na uzgodnionym kanale (slug hipotezy albo eksperymentu).
2. Dziecko czeka przez `wait_for_updates` na tym kanale.
3. Rodzic odpowiada na kanale i/lub aktualizuje `description`.
4. Dziecko kontynuuje w tej samej sesji z wyjaśnionego kanału/`description`.

## Wiadomość vs plik

Krótki wniosek może być w wiadomości. Log, diff, tabela, długi wynik → plik; w wiadomości tylko ścieżka. Styl: zwykły, krótki tekst.

## Notatki i literatura

Notatki `ai-crew-sync` (`scope`/`key`) — treść poza jednym węzłem. Treść węzła → jego `description`. Spis literatury: `literature/index.md` pod lockiem (patrz `roles/librarian.md`).
