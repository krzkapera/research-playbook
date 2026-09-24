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

`description` edytuje wyłącznie właściciel etapu: `professor` (hipoteza), `laborant` (eksperyment). Inne role oddają materiał laborantowi na kanale (albo w odpowiedzi spawnu); pętlę programmer↔operator prowadzą przez `ask_agent`. Właściciel wciąga materiał z kanału do `description`.

## Pokój (recenzja)

Pokój = recenzja **gotowego** draftu z `description`. Synonim w `roles/`: **pętla** / **runda recenzji** (np. pętla z criticiem). Skład rośnie stopniowo (kolejna osoba → kolejna runda). Właściciel etapu ma głos rozstrzygający przy braku zgody. Pokój kończy się, gdy wracasz do solo albo zmieniasz etap.

## Czekanie

Czekaj przez `wait_for_updates` z `channel: "<slug>"`. Spawn zawsze z `--no-wake`.

Po spawnie z `--no-wake` rodzic **nie** dostaje budzenia z odpowiedzi spawnu. Oddanie dziecka (uwagi, raport, synteza) idzie na uzgodniony kanał; rodzic odbiera je przez `wait_for_updates` na tym kanale.

## P2P między helperami (programmer ↔ operator)

`ask_agent` (po `list_agents`) to kanał roboczy między żywymi sesjami helperów tego samego eksperymentu.

- **Programmer ↔ operator:** pętla naprawcza kodu, diagnoza logów, prośba o commit — wyłącznie przez `ask_agent`.
- **Spawn operatora**: robi zawsze **programmer** (nie laborant).
- **Kanał eksperymentu** zostawiasz na sygnały dla laboranta: gotowość kodu (programmer), policzone wyniki / status końcowy (operator), roundtrip designu z laborantem, recenzja z criticiem.
- Operator przy błędzie implementacji najpierw naprawia sam; gdy utknie — `ask_agent` do programisty. Programmer po gotowości (i po spawnie operatora) trzyma sesję na `wait_for_updates`, żeby móc odebrać `ask_agent`.

## Zadanie vs dyskusja


- Robota z postępem węzła → zadanie `ai-crew-sync` (`create_task` / `claim_task`), może mieć właściciela i zależności.
- Krytyka, pytania, propozycje → zwykła dyskusja na kanale (bez claim).

`create_task`, gdy pulę może wziąć którykolwiek z równoważnych aktywnych agentów (`list_agents`). Spawn dedykowanego helpera: brief spawnu wystarcza za zadanie.

## Spawn

- Przy każdym `orx agent spawn` zawsze `--no-wake`.
- Równoległe spawny OK; koordynacja na kanałach.

1. Sprawdź `list_agents`. `ask_agent` — do żywej sesji helpera **tego samego** kontekstu (ten sam eksperyment / ta sama pętla), gdy plik roli przewiduje P2P (np. operator ↔ programmer). Nowy węzeł albo nowa dedykowana sesja wg pliku roli → `orx agent spawn` wg szablonu w `roles/` (np. laborant: nowy programmer na eksperyment), także gdy w projekcie widać inną sesję tej samej roli.
2. Przy każdym `orx agent spawn` zawsze podaj `--no-wake`. Szczegóły flagi: skill `/orx-agent-delegation`.
3. Brief: kanały do natychmiastowego dołączenia, rola wprost, zadanie, oczekiwany wynik. Spawn: komenda z `model-assignment.md` dla danej roli (brief w miejsce "<task>"). Szablony briefów są w `roles/`. Opis flag: `orx-agent-spawn-options.md`.
4. Koordynacja równoległych dzieci = kanały (hipoteza / eksperyment). Kanał jest źródłem prawdy o trwającej pracy.
5. Kończąc: materiał oddaj na uzgodnionym kanale (to budzi rodzica siedzącego na `wait_for_updates`). Opcjonalna krótka odpowiedź spawnu ≤ ~4000 znaków jest skrótem pomocniczym — przy `--no-wake` sama nie budzi rodzica. Bieżący stan: kanał i `description`.

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
