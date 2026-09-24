# Wspólna komunikacja

## Pojęcia

- **Bus** — `ai-crew-sync`: kanały, wiadomości i P2P, obsługiwane narzędziami MCP własnej sesji (tożsamość: `agent-start.md`, krok 1).
- **Kanał węzła** — kanał nazwany slugiem hipotezy albo eksperymentu. Cała koordynacja pracy nad węzłem idzie na jego kanale.
- **Dołączenie do kanału** — odczyt historii kanału (`read_messages`, `scope: "<slug>"`, `only_new: false`) i dalsza praca na nim. Zaproszenie = nazwa kanału w briefie spawnu albo we wpisie.
- **Wpis** — wiadomość na kanale (`post_message`, `channel: "<slug>"`). Każdy wpis zaczyna się etykietą roli: `[professor]`, `[laborant]`, `[critic]`, `[programmer]`, `[operator]`, `[librarian]`.
- **P2P** — wiadomości bezpośrednie między sesjami programmera i operatora: pytanie `ask_agent`, odpowiedź `post_message` (sekcja P2P).
- **Adres P2P** — `<agent>/<session>` z wyniku `whoami` sesji (`agent-start.md`, krok 1).
- **Oddanie** — wpis albo wiadomość P2P z materiałem, na który czeka inna rola. Zawartość oddania definiuje plik roli oddającej (sekcja Co oddajesz).
- **`description`** — pole węzła w `orx`; źródło prawdy o stanie i decyzjach węzła.
- **Właściciel etapu** — `professor` (hipoteza), `laborant` (eksperyment).
- **Problem z flow** — sytuacja z listy w sekcji Problem z flow.

## Kanały

- Kanał węzła zakłada twórca węzła po `orx create-experiment`: nazwa kanału = slug wypisany przez tę komendę (linia `slug:`). Po założeniu twórca dołącza do kanału i pisze pierwszy wpis.
- Dołączasz do kanałów wskazanych w briefie i do kanału węzła, w którym masz aktywną rolę.
- Draft solo nie wymaga innych na kanale. Zaproszenie innych na kanał otwiera rundę recenzji.

## P2P (programmer ↔ operator)

- Cała wymiana programmer ↔ operator idzie przez P2P: potwierdzenie startu, dopytania o uruchomienie, statusy, prośby o poprawkę kodu, raport operatora i odpowiedzi programmera. Operator pisze na kanale eksperymentu wyłącznie Problem z flow i konflikt z `description` (`agent-start.md`, krok 7).
- Operator pyta, programmer odpowiada. Programmer podaje swój adres P2P w briefie spawnu operatora; adres operatora bierze z pól `from` i `from_session` jego pierwszej wiadomości.
- **Pytanie (operator):** `ask_agent` z `to: "<adres P2P programmera>"`, `question` i `timeout_seconds: 86400`. `answered: true` → odpowiedź w polu `answer`. Ponawianie: sekcja Czekanie.
- **Odpowiedź (programmer):** pytanie przychodzi jako wiadomość bezpośrednia w pętli czekania (sekcja Czekanie). Odpowiadasz na każde pytanie: `post_message` z `to: "<from>/<from_session>"` i `reply_to: <id pytania>`.

## Opis węzła vs wpis

`description` edytuje wyłącznie właściciel etapu i aktualizuje go na bieżąco (odczyt → nadpisanie całości). Wpis to krótka delta: co się zmieniło i o co chodzi. Historia kanału jest archiwum dyskusji. Inne role oddają materiał właścicielowi etapu na kanale węzła; właściciel przenosi ustalenia do `description`.

## Pokój (recenzja)

Pokój = recenzja **gotowego** draftu z `description`. Synonim w `roles/`: **pętla** / **runda recenzji** (np. pętla z criticiem). Skład rośnie stopniowo (kolejna osoba → kolejna runda). Właściciel etapu ma głos rozstrzygający przy braku zgody. Pokój kończy się, gdy wracasz do solo albo zmieniasz etap.

## Czekanie

- **Na wpis albo wiadomość P2P:** `wait_for_updates` z `channel: "<slug>"` i `timeout_seconds: 86400`, potem `read_messages` z `scope: "all"` i `only_new: true`. Wiadomość bezpośrednia do Twojej sesji (P2P) także budzi to wywołanie.
- **Na odpowiedź na własne pytanie P2P:** `ask_agent` z `timeout_seconds: 86400` (sekcja P2P).
- **Na lock:** `wait_for_updates` bez `channel`, z `timeout_seconds: 86400`, potem `read_messages` jak wyżej i ponowne `acquire_lock`.

Wywołanie wraca bez oczekiwanego oddania, z timeoutem albo z błędem klienta → wywołujesz je ponownie; `ask_agent` z `resume_message_id: <question_message_id>`, bez `question`.

Stan pracy innych agentów odczytujesz wyłącznie z wpisów na kanale i wiadomości P2P. `list_agents` i obecność (presence) nie są sygnałem, czy agent pracuje.

Rodzic zostaje w turze, dopóki spawnowane przez niego dzieci pracują nad jego węzłem. Turę kończy po nadejściu oddania albo po Problemie z flow.

Odpowiedź spawnu dziecka nie budzi rodzica; oddanie przychodzi wpisem na kanale albo wiadomością P2P.

## Problem z flow

Problem z flow to:

- błąd busa: MCP `ai-crew-sync` się nie ładuje albo narzędzie zwraca błąd poza wywołaniami czekania (sekcja Czekanie);
- błąd środowiska: narzędzie, komenda (`orx`, `git`, `ssh`) albo usługa zwraca błąd uwierzytelnienia, uprawnień, konfiguracji albo niedostępności;
- wynik `whoami` niezgodny z `agent-start.md` (krok 1);
- nieudany spawn: `orx agent spawn` nie wypisuje `Spawned agent session …`;
- brak decyzji od roli, do której ta decyzja należy.

Działanie:

1. Gdy bus działa i `whoami` jest poprawne: wpis na kanale węzła `[<rola>] Problem z flow: <co>; <komenda>; <dokładny błąd>`.
2. To samo w odpowiedzi do rodzica albo użytkownika.
3. Koniec tury.

Konfiguracja środowiska i projektu jest tylko do odczytu: konfiguracje harnessu i MCP, tokeny, pliki env, usługi, bus, ustawienia `orx`, konfiguracja i hooki git repozytorium projektu. Błąd busa albo środowiska obsługujesz wyłącznie krokami Działania.

Decyzję należącą do innej roli podejmuje wyłącznie ta rola.

## Spawn

- `orx agent` ma dwie komendy: `spawn` i `kill`. Postęp dziecka śledzisz na kanale węzła; postęp operatora — w P2P.
- Komenda spawnu: wiersz roli z `model-assignment.md`; brief z pliku (`identifiers.md` § Miejsca zapisu) przez `--stdin`.
- Szablon briefu jest w pliku roli, która spawnuje. Brief zawiera wyłącznie dane tej sesji: rolę, `project_id`, węzeł (`id`, slug), kanał, zadanie specyficzne dla tej sesji, limity z briefu użytkownika oraz oddanie (co i gdzie: kanał albo adres P2P odbiorcy). Brief odsyła do `agent-start.md` i pliku roli; reguły z playbooka zostają w plikach playbooka.
- Limity z briefu użytkownika przekazujesz w briefie dziecka dosłownie, z rolą, której dotyczą.
- Równoległe spawny: każde dziecko pracuje na kanale swojego węzła.
- Nowy węzeł albo nowa dedykowana sesja według pliku roli = nowy spawn według szablonu, także gdy w projekcie widać inną sesję tej samej roli.

## Roundtrip (niejasny brief)

Gdy zlecenie (brief, `description`, dokument, do którego zlecenie odsyła) nie wystarcza do kontynuacji, dziecko dopytuje nadawcę zlecenia w **tej samej** sesji, drogą, którą przyszło zlecenie: operator — programmera przez P2P (sekcja P2P); pozostałe role — na kanale węzła.

1. Dziecko zadaje pytania tą drogą.
2. Dziecko czeka na odpowiedź (sekcja Czekanie).
3. Nadawca odpowiada tą samą drogą i/lub aktualizuje `description`.
4. Dziecko kontynuuje w tej samej sesji z wyjaśnionej odpowiedzi/`description`.

## Wiadomość vs plik

Krótki wniosek mieści się we wpisie. Log, diff, tabela, wykres, długi wynik → plik w miejscu według `identifiers.md` § Miejsca zapisu; we wpisie ścieżka albo link `artifacts/<slug>/…`. Styl: zwykły, krótki tekst.

## Literatura

Korpus i spis literatury: `<repo>/literature/` (`identifiers.md` § Miejsca zapisu).
