# Wspólna komunikacja

Narzędzie: `ai-crew-sync` — wiadomości, P2P (`ask_agent`), kanały, zadania, locki, `wait_for_updates`. Stan węzła żyje w `description` (`orx`), nie na kanale.

## Kanały

- Zawsze dołączaj do `project`.
- Do kanału sluga węzła dołączaj tylko przy aktywnej roli *teraz* (właściciel etapu, zaproszenie do recenzji, albo zlecenie wskazuje ten slug).
- Bez dołączania na zapas.
- Draft solo nie wymaga innych na kanale. Wciąganie ludzi = start recenzji, nie start pisania draftu.

Kanał o nazwie sluga powstaje, gdy twórca węzła dołączy i napisze pierwszą wiadomość. Zaproszenie = podanie nazwy kanału w briefie albo P2P.

## Opis węzła vs wiadomość

`description` jest źródłem prawdy o stanie i decyzjach — aktualizuj na bieżąco. Wiadomość na kanale to krótka delta: co się zmieniło i o co chodzi. Historia kanału = archiwum „dlaczego”.

`description` edytuje wyłącznie właściciel etapu: `professor` (hipoteza), `laborant` (eksperyment). Inne role oddają materiał na kanale lub w odpowiedzi spawnu; właściciel wciąga go do `description`.

## Pokój (recenzja)

Pokój = recenzja **gotowego** draftu z `description`. Skład rośnie stopniowo (kolejna osoba → kolejna runda). Właściciel etapu ma głos rozstrzygający przy braku zgody. Brak formalnego zamknięcia — pokój przestaje być używany, gdy wracasz do solo albo zmieniasz etap.

## Czekanie

Zamiast pollingu: `wait_for_updates` z `channel: "<slug>"`. Po `orx agent spawn` rodzic dostaje wake przy zamknięciu dziecka (chyba że `--no-wake`; skill `/orx-agent-delegation`).

## Zadanie vs dyskusja

- Robota z postępem węzła → zadanie `ai-crew-sync` (`create_task` / `claim_task`), może mieć właściciela i zależności.
- Krytyka, pytania, propozycje → zwykła dyskusja na kanale (bez claim).

`create_task`, gdy pulę może wziąć którykolwiek z równoważnych aktywnych agentów (`list_agents`). Spawn dedykowanego helpera: brief spawnu wystarcza za zadanie.

## Spawn

1. Sprawdź `list_agents` — gdy potrzebna rola już działa w tym kontekście, użyj `ask_agent`. Wyjątki: plik roli, która spawnuje.
2. Brief jest zaproszeniem: kanały do natychmiastowego dołączenia, rola wprost, zadanie, oczekiwany wynik. Zawsze `--harness` i `--model` (`model-assignment.md` / szablon w pliku roli). Szablony briefów są w `roles/`, nie tutaj.
3. Odpowiedź spawnu do rodzica ≤ ~4000 znaków. Dłuższy materiał → kanał ze ścieżkami; w odpowiedzi spawnu skrót + odesłanie.

Szablon briefu:

```text
Jesteś <rola> dla projektu <project_id>. Przeczytaj `roles/<plik-roli>.md` i kieruj się nim.

Slug hipotezy/eksperymentu: <slug, jeśli dotyczy>
Kanały dołącz natychmiast: project[, <slug>]
Zadanie: <konkretne, samodzielne>
Oczekiwany wynik: <co i w jakiej formie>
```

Dziecko kończąc: (1) krótka odpowiedź spawnu, (2) ścieżki/logi/tabele na uzgodnionym kanale. Właściciel etapu wciąga to do `description`.

## Roundtrip (niejasny brief, rodzic śpi)

1. Pytania na uzgodnionym kanale.
2. Koniec sesji z `BLOCKED: potrzebuję wyjaśnienia` + pytania (wake rodzica).
3. Rodzic uzupełnia brief/`description` i robi re-spawn (albo `ask_agent`, gdy dziecko żyje i plik roli rodzica na to pozwala).
4. Dziecko w nowej sesji kontynuuje bez domysłów.

## Wiadomość vs plik

Krótki wniosek może być w wiadomości. Log, diff, tabela, długi wynik → plik; w wiadomości tylko ścieżka. Styl: zwykły, krótki tekst.

## Notatki i literatura

Notatki `ai-crew-sync` (`scope`/`key`) — treść poza jednym węzłem. Treść węzła → jego `description`. Spis literatury: `literature/index.md` pod lockiem (patrz `roles/librarian.md`).
