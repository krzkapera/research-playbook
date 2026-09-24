# Wspólna komunikacja

## Pojęcia

- **Bus** — `ai-crew-sync`: kanały i wiadomości, obsługiwane narzędziami MCP własnej sesji (tożsamość: `agent-start.md`, krok 1).
- **Kanał węzła** — kanał nazwany slugiem hipotezy albo eksperymentu. Cała koordynacja pracy nad węzłem idzie na jego kanale.
- **Dołączenie do kanału** — odczyt historii kanału (`read_messages`, `scope: "<slug>"`, `only_new: false`) i dalsza praca na nim. Zaproszenie = nazwa kanału w briefie spawnu albo we wpisie.
- **Wpis** — wiadomość na kanale (`post_message`, `channel: "<slug>"`). Każdy wpis zaczyna się etykietą roli: `[professor]`, `[laborant]`, `[critic]`, `[programmer]`, `[operator]`, `[librarian]`.
- **Oddanie** — wpis z materiałem, na który czeka inna rola. Zawartość oddania definiuje plik roli oddającej (sekcja Co oddajesz).
- **`description`** — pole węzła w `orx`; źródło prawdy o stanie i decyzjach węzła.
- **Właściciel etapu** — `professor` (hipoteza), `laborant` (eksperyment).
- **Problem z flow** — sytuacja z listy w sekcji Problem z flow.

## Kanały

- Kanał węzła zakłada twórca węzła po `orx create-experiment`: nazwa kanału = slug wypisany przez tę komendę (linia `slug:`). Po założeniu twórca dołącza do kanału i pisze pierwszy wpis.
- Dołączasz do kanałów wskazanych w briefie i do kanału węzła, w którym masz aktywną rolę.
- Draft solo nie wymaga innych na kanale. Zaproszenie innych na kanał otwiera rundę recenzji.

## Opis węzła vs wpis

`description` edytuje wyłącznie właściciel etapu i aktualizuje go na bieżąco (odczyt → nadpisanie całości). Wpis to krótka delta: co się zmieniło i o co chodzi. Historia kanału jest archiwum dyskusji. Inne role oddają materiał właścicielowi etapu na kanale węzła; właściciel przenosi ustalenia do `description`.

## Pokój (recenzja)

Pokój = recenzja **gotowego** draftu z `description`. Synonim w `roles/`: **pętla** / **runda recenzji** (np. pętla z criticiem). Skład rośnie stopniowo (kolejna osoba → kolejna runda). Właściciel etapu ma głos rozstrzygający przy braku zgody. Pokój kończy się, gdy wracasz do solo albo zmieniasz etap.

## Czekanie

Czekanie na oddanie to pętla:

1. `wait_for_updates` z `channel: "<slug>"` i `timeout_seconds` ≤ 50.
2. Po każdym obudzeniu: `read_messages` z `scope: "all"` i `only_new: true`.
3. Oczekiwane oddanie przyszło → dalej według flow. Pusty odczyt po obudzeniu albo timeout wywołania → wróć do kroku 1.
4. Minął maksymalny łączny czas z tabeli bez oddania → Problem z flow.

Stan pracy innych agentów odczytujesz wyłącznie z wpisów na kanale. `list_agents` i obecność (presence) nie są sygnałem, czy agent pracuje.

Rodzic zostaje w turze, dopóki spawnowane przez niego dzieci pracują nad jego węzłem. Turę kończy po nadejściu oddania, po upływie maksymalnego czasu (wtedy zgłasza Problem z flow) albo po innym Problemie z flow.

Odpowiedź spawnu dziecka nie budzi rodzica; oddanie przychodzi wpisem na kanale.

Maksymalny łączny czas liczysz od spawnu albo od ostatniego wpisu na kanale, na którym czekasz (późniejsze z nich):

| Na co czekasz | Kto czeka | Maks. łączny czas |
|---|---|---|
| odpowiedź w rozmowie na kanale: uwagi w fazie treści, odniesienie do uwag, roundtrip, dopytanie, potwierdzenie przyjęcia | każda rola | 15 min |
| uwagi critica po spawnie | professor, laborant | 15 min |
| decyzja professora: „gotowa do weryfikacji”, decyzja po skrócie analizy | laborant | 15 min |
| synteza librariana po spawnie | zlecający | 60 min |
| ogłoszenie eksperymentu po decyzji „gotowa do weryfikacji” albo po kolejnym pytaniu | professor | 1 h |
| gotowość kodu po spawnie programmera | laborant | 2 h |
| poprawka kodu po prośbie operatora | operator | 1 h |
| raport operatora po spawnie albo po zleceniu kolejnego runu | programmer | 24 h |
| wynik eksperymentu po gotowości kodu | laborant | 30 h |
| skrót analizy po ogłoszeniu eksperymentu | professor | 36 h |

## Problem z flow

Problem z flow to:

- błąd busa: MCP `ai-crew-sync` się nie ładuje albo narzędzie zwraca błąd;
- wynik `whoami` niezgodny z `agent-start.md` (krok 1);
- nieudany spawn: `orx agent spawn` nie wypisuje `Spawned agent session …`;
- upływ maksymalnego czasu czekania bez oddania (sekcja Czekanie);
- brak decyzji od roli, do której ta decyzja należy.

Działanie:

1. Gdy bus działa i `whoami` jest poprawne: wpis na kanale węzła `[<rola>] Problem z flow: <co>; <komenda>; <dokładny błąd>`.
2. To samo w odpowiedzi do rodzica albo użytkownika.
3. Koniec tury.

Decyzję należącą do innej roli podejmuje wyłącznie ta rola.

## Spawn

- `orx agent` ma dwie komendy: `spawn` i `kill`. Postęp dziecka śledzisz na kanale węzła.
- Komenda spawnu: wiersz roli z `model-assignment.md`; brief z pliku przez `--stdin`.
- Szablon briefu jest w pliku roli, która spawnuje. Brief zawiera wyłącznie dane tej sesji: rolę, `project_id`, węzeł (`id`, slug), kanał, zadanie specyficzne dla tej sesji, limity z briefu użytkownika oraz oddanie (co i na którym kanale). Brief odsyła do `agent-start.md` i pliku roli; reguły z playbooka zostają w plikach playbooka.
- Limity z briefu użytkownika przekazujesz w briefie dziecka dosłownie, z rolą, której dotyczą.
- Równoległe spawny: każde dziecko pracuje na kanale swojego węzła.
- Nowy węzeł albo nowa dedykowana sesja według pliku roli = nowy spawn według szablonu, także gdy w projekcie widać inną sesję tej samej roli.

## Roundtrip (niejasny brief)

Gdy brief albo `description` nie wystarcza do kontynuacji, doprecyzowanie idzie przez kanał w **tej samej** sesji dziecka:

1. Dziecko publikuje pytania na kanale węzła.
2. Dziecko czeka na odpowiedź (sekcja Czekanie).
3. Rodzic odpowiada na kanale i/lub aktualizuje `description`.
4. Dziecko kontynuuje w tej samej sesji z wyjaśnionego kanału/`description`.

## Wiadomość vs plik

Krótki wniosek mieści się we wpisie. Log, diff, tabela, wykres, długi wynik → plik w miejscu według `identifiers.md` § Miejsca zapisu; we wpisie ścieżka albo link `artifacts/<slug>/…`. Styl: zwykły, krótki tekst.

## Literatura

Spis literatury: `literature/index.md` (`roles/librarian.md`).
