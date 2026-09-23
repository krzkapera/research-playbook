# Wspólna komunikacja

Każdy agent dołącza do `project`. Każda aktywna hipoteza i każdy aktywny eksperyment ma kanał nazwany swoim slugiem z `orx` — to pokój do rozmowy o tym węźle, nie magazyn stanu (patrz "Pokój" niżej). P2P służy sprawom skierowanym do konkretnego agenta. `ai-crew-sync` zapewnia wiadomości, delegowanie (`ask_agent`), kanały, zadania, locki i blokujące czekanie (`wait_for_updates`).

## Znalezienie i zaadresowanie konkretnego agenta

Gdy potrzebujesz kogoś znaleźć, wołaj `list_agents` — pokazuje, kto jest aktywny i nad czym pracuje. 
## Opis węzła jako źródło prawdy

`description` węzła jest samowystarczalną dokumentacją — ma wystarczać do zrozumienia stanu i decyzji nawet komuś, kto nie widział rozmowy: nowemu uczestnikowi, ale też właścicielowi wracającemu do tematu po przerwie albo prowadzącemu wiele równoległych wątków naraz (np. professor). Aktualizuje się go na bieżąco, w trakcie rozmowy w pokoju, a nie dopiero na koniec — bo ta rozmowa istnieje właśnie po to, żeby dopracować ten opis (patrz "Kto edytuje węzeł" niżej).

Skoro opis jest zawsze aktualny, wiadomość w pokoju to krótka delta: co się zmieniło w opisie i o co się pyta. Historia kanału jest archiwum „dlaczego tak zdecydowano”; sięgasz do niej, gdy potrzebujesz kontekstu decyzji.

## Dołączanie do kanałów

- Każdy agent zawsze dołącza do `project`.
- Do kanału nazwanego slugiem węzła dołączasz **tylko**, gdy masz w tym węźle aktywną rolę *teraz*: jesteś właścicielem etapu, zostałeś zaproszony do recenzji, albo Twoje zlecenie wskazuje ten slug i oczekuje udziału.
- Nie dołączaj do kanałów hipotez/eksperymentów „na zapas" ani „na wszelki wypadek".
- Samotne draftowanie nie wymaga obecności innych na kanale. Właściciel etapu może być na kanale sam albo wejść na niego dopiero przy zaproszeniu do recenzji — obie opcje są poprawne; zabronione jest wciąganie innych zanim jest draft.

## Pokój: recenzja, nie start pracy

„Otwarcie pokoju" oznacza **zaproszenie innych do recenzji gotowego draftu** — draft powstaje wcześniej w pracy solo właściciela etapu. Właściciel etapu najpierw pracuje sam i zapisuje wynik w `description` węzła; dopiero potem zaprasza kolejnych uczestników wiadomością z nazwą kanału — `ai-crew-sync` nie ma ACL na kanały, więc „zaproszenie" to przekazanie nazwy. Fazę hipotezy prowadzą `professor`, `laborant` i `critic` na kanale hipotezy. Fazę eksperymentów prowadzi `laborant` z `critic` przypisanym do węzła eksperymentu (osobna sesja na węzeł; ta sama sesja może ocenić design i wyniki tego węzła).

Skład pokoju rośnie stopniowo: każde dołączenie nowej osoby otwiera nową rundę iteracji, aż do wyczerpania uwag albo zamknięcia etapu. Runda trwa, aż nikt nie ma więcej uwag; wtedy albo dołącza kolejna osoba i iteracja zaczyna się od nowa, albo etap jest zamknięty. Właściciel etapu (professor dla hipotezy, laborant dla eksperymentu, programmer dla implementacji) ma głos decydujący, gdy uwagi nie prowadzą do zgody.

Pokój nie ma formalnego zamknięcia — po prostu przestaje być używany, gdy praca schodzi do fazy solo albo przechodzi do kolejnego etapu (zwykłe zadanie, patrz „Zadanie czy dyskusja" niżej). Historia zostaje jako trwały zapis.

## Czekanie zamiast odpytywania

Zamiast odpytywać kanał w pętli, wołający blokuje się na `wait_for_updates` z `channel: "<slug>"` — czeka na odpowiedź w konkretnym pokoju, nie na cały ruch zespołu; nadpisuje domyślne filtrowanie po kanale sesji i po `kinds`. Limit czasu nie ma górnej granicy — można czekać długo zamiast odświeżać w pętli.

Na zakończenie zleconego zadania (nie rozmowy w pokoju) nie czeka się przez `ai-crew-sync` — `orx agent spawn` sam wybudza sesję-rodzica z odpowiedzią helpera, gdy jego sesja się kończy (chyba że użyto `--no-wake`; szczegóły w natywnym skillu `/orx-agent-delegation`, patrz niżej); osobny mechanizm czekania na `task_key` w `ai-crew-sync` okazał się zbędny i został wycofany. Ta odpowiedź jest ucięta po ok. 4000 znakach — patrz "Delegowanie do innej sesji" niżej.

## Delegowanie do innej sesji

Zanim zaczniesz nową sesję, sprawdź `list_agents`: jeśli potrzebna rola już działa w tym kontekście, napisz do niej P2P (`ask_agent`) zamiast spawnować kolejną. Dopiero gdy nikt taki nie jest aktywny, użyj `orx agent spawn`. Wyjątki od ponownego użycia sesji opisuje plik roli, która spawnuje.

Przed użyciem `orx agent spawn` przeczytaj natywny skill `/orx-agent-delegation` — tam jest składnia, ochrona brancha, `--no-wake`, sprzątanie (`orx agent kill`). Tutaj zostają reguły zespołu i szablony briefów.

Jednego ten natywny skill nie wie: nowa sesja nie ma żadnej domyślnej roli. Zlecający musi ją wskazać wprost w treści zadania, inaczej helper nie będzie wiedział, kim ma być. Szablon brief-u (`--stdin` dla wieloliniowego):

```text
Jesteś <rola> dla projektu <project_id>. Przeczytaj `roles/<plik-roli>.md` i kieruj się nim.

Slug hipotezy/eksperymentu: <slug, jeśli dotyczy>
Zadanie: <konkretne, samodzielne zadanie — helper nie widzi tej rozmowy>
Oczekiwany wynik: <co i w jakiej formie oddać>
```

Odpowiedź, którą wybudzona sesja-rodzic dostaje z zamknięcia helpera, jest ucięta na ok. 4000 znakach, bez ostrzeżenia i bez łatwego sposobu odzyskania reszty. Jeśli spodziewasz się dłuższej odpowiedzi (np. syntezy z szerokiego przeglądu literatury), poinstruuj helpera w briefie: zmieść syntezę w tym limicie (najważniejsze wyżej), a jeśli się nie mieści — niech pełną wersję wyśle jako wiadomość na właściwy kanał (patrz "Próg: wiadomość czy plik" niżej), a w zamkniętej odpowiedzi zostawi tylko krótkie odesłanie tam.

Przy każdym `orx agent spawn` podaj `--harness` i `--model` z szablonu poniżej (źródło: `model-assignment.md`). Bez flag dziecko dziedziczy Twój harness/model — najczęstszy błąd: laborant (Claude Code / Opus) spawnuje critica bez `--harness cursor`. Nazwa harnessu jest stała; nazwa modelu na części harnessów zmienia się w czasie — gdy w szablonie jest placeholder, sprawdź aktualną wartość narzędziem harnessu (np. `agy models`, `opencode models`) zamiast zgadywać.

## Próg: wiadomość czy plik

Wiadomości mają być jak najkrótsze. Pojedyncza liczba albo jedno zdanie wniosku może zostać wprost w wiadomości (albo w opisie). Wszystko dłuższe — log, diff, tabela, pełny wynik — idzie do pliku/attachmentu, a wiadomość niesie tylko ścieżkę.

Wiadomości pisz zwykłym, swobodnym, krótkim tekstem.

## Zadanie czy dyskusja

Konkretna robota prowadząca do postępu węzła (implementacja, uruchomienie, analiza) to zadanie w kolejce `ai-crew-sync` (`create_task`/`claim_task`) — ma właściciela, może mieć `depends_on`. Krytyka, pytania i propozycje to luźna dyskusja w kanale — nikt jej nie "claimuje", nikt nie jest za nią formalnie odpowiedzialny. Jedynymi stałymi elementami są hipoteza i eksperyment same w sobie, nie role wokół nich.

`create_task`/`claim_task` i `orx agent spawn` to dwa niezależne w `orx` mechanizmy. Użyj `create_task`, gdy zadanie trafia do wspólnej puli, którą może odebrać którykolwiek z kilku już aktywnych, równoważnych agentów (patrz `list_agents`) — wtedy oni sami je `claim_task`/`claim_next_task`-ują. Gdy zamiast tego spawnujesz dedykowanego pomocnika do jednej konkretnej roboty (patrz niżej), sam brief ze spawnu wystarcza za zadanie.

## Kto edytuje węzeł

`description` edytuje wyłącznie aktualny właściciel etapu: `professor` na poziomie hipotezy, `laborant` na poziomie eksperymentu. Programmer, operator, critic i librarian oddają materiał na kanale albo w odpowiedzi spawnu; właściciel etapu wciąga go do `description`. To rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz.

## Notatki

`ai-crew-sync` notes (`scope`/`key`, pełnotekstowe wyszukiwanie) są dla treści nieprzypisanej do jednego węzła — np. przekrojowe decyzje projektu. Treść należącą do konkretnego węzła trzymaj w jego `description`. Spis literatury (`literature/index.md`) to zwykły plik chroniony lockiem `ai-crew-sync` (patrz `roles/librarian.md`).

## Jak powstaje kanał

`ai-crew-sync` nie ma osobnego ACL „create channel”: kanał o nazwie sluga powstaje, gdy twórca węzła **dołączy i napisze pierwszą wiadomość** pod tą nazwą. Zaproszenie innych = podanie nazwy kanału w briefie spawnu albo P2P.


## Roundtrip: dziecko pyta śpiącego rodzica

Gdy sesja powstała przez `orx agent spawn`, rodzic zwykle czeka na wake. Dziecko z niejasnym briefem dopytuje przez roundtrip: pytania na kanale, potem `BLOCKED: potrzebuję wyjaśnienia` w odpowiedzi spawnu (wake rodzica).

Protokół:

1. Dziecko pisze pytania na uzgodnionym kanale (krótko).
2. Dziecko **kończy sesję** z odpowiedzią spawnu: `BLOCKED: potrzebuję wyjaśnienia` + pytania (to jest wake rodzica).
3. Rodzic po wake uzupełnia `description` / brief i robi re-spawn z odpowiedziami (albo `ask_agent`, gdy rola-dziecko nadal żyje i plik roli rodzica na to pozwala).
4. Dziecko w nowej sesji kontynuuje — bez domysłów z poprzedniej blokady.

Ten sam wzorzec wolno użyć przy każdym spawnie, gdy brief jest niejasny.

## Spawn: reguły wspólne

Brief spawnu (`orx agent spawn`, zwykle przez stdin) **jest zaproszeniem**: wymień w nim kanały do natychmiastowego dołączenia. Wskaż rolę wprost (nowa sesja nie ma domyślnej). Przy każdym spawnie podaj `--harness` i `--model` z `model-assignment.md` / szablonu w pliku roli, która spawnuje. Szablony briefów są w plikach ról w `roles/`, nie tutaj.

Odpowiedź z zamknięcia helpera do rodzica jest ucięta ok. 4000 znaków — dłuższy materiał na kanał, w odpowiedzi spawnu tylko skrót i ścieżki.

### Zwrot wyniku (wake + kanał)

Sesja-dziecko kończąc pracę: (1) krótka odpowiedź spawnu dla rodzica (≤ ~4000 znaków), (2) jeśli są ścieżki/logi/tabele — wiadomość na uzgodnionym kanale z ścieżkami. Właściciel etapu wciąga to do `description`.
