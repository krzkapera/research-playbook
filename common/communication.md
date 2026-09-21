# Wspólna komunikacja

Każdy agent dołącza do `project`. Każda aktywna hipoteza i każdy aktywny eksperyment ma kanał nazwany swoim slugiem z `orx` — to pokój do rozmowy o tym węźle, nie magazyn stanu (patrz "Pokój" niżej). P2P służy sprawom skierowanym do konkretnego agenta. `ai-crew-sync` zapewnia wiadomości, delegowanie (`ask_agent`), kanały, zadania, locki i blokujące czekanie (`wait_for_updates`).

## Znalezienie i zaadresowanie konkretnego agenta

Publikuj, nad czym aktualnie pracujesz, przez `heartbeat` (pole `activity`) — wymień w nim slug hipotezy/eksperymentu, żeby inni Cię znaleźli, np. "laborant, lora-rank-vs-shots". Zanim napiszesz P2P do kogoś konkretnego (np. "laborant obsługujący tę hipotezę"), sprawdź `list_agents` — pokazuje, kto jest aktywny i nad czym pracuje. Zaadresuj `ask_agent(to: "<handle>")`, albo `"<handle>/<sesja>"`, gdy chodzi o konkretny wątek pracy tej osoby, nie o nią w ogóle.

## Opis węzła jako źródło prawdy

`description` węzła jest samowystarczalną dokumentacją — ma wystarczać do zrozumienia stanu i decyzji nawet komuś, kto nie widział rozmowy: nowemu uczestnikowi, ale też właścicielowi wracającemu do tematu po przerwie albo prowadzącemu wiele równoległych wątków naraz (np. professor). Aktualizuje się go na bieżąco, w trakcie rozmowy w pokoju, a nie dopiero na koniec — bo ta rozmowa istnieje właśnie po to, żeby dopracować ten opis (patrz "Kto edytuje węzeł" niżej).

Skoro opis jest zawsze aktualny, wiadomość w pokoju nie powtarza tego, co w nim już jest. Wiadomość to krótka delta: co się zmieniło w opisie i o co się pyta — nie treść merytoryczna od nowa. Historia kanału jest archiwum "dlaczego tak zdecydowano", do którego sięga się na żądanie, nigdy wymaganą lekturą.

## Dołączanie do kanałów

- Każdy agent zawsze dołącza do `project`.
- Do kanału nazwanego slugiem węzła dołączasz **tylko**, gdy masz w tym węźle aktywną rolę *teraz*: jesteś właścicielem etapu, zostałeś zaproszony do recenzji, albo Twoje zlecenie wskazuje ten slug i oczekuje udziału.
- Nie dołączaj do kanałów hipotez/eksperymentów „na zapas" ani „na wszelki wypadek".
- Samotne draftowanie nie wymaga obecności innych na kanale. Właściciel etapu może być na kanale sam albo wejść na niego dopiero przy zaproszeniu do recenzji — obie opcje są poprawne; zabronione jest wciąganie innych zanim jest draft.

## Pokój: recenzja, nie start pracy

„Otwarcie pokoju" oznacza **zaproszenie innych do recenzji gotowego draftu**, nie rozpoczęcie pracy nad czymś nowym. Właściciel etapu najpierw pracuje sam i zapisuje wynik w `description` węzła; dopiero potem zaprasza kolejnych uczestników wiadomością z nazwą kanału — `ai-crew-sync` nie ma ACL na kanały, więc „zaproszenie" to przekazanie nazwy.

Skład pokoju rośnie stopniowo, nie od razu w komplecie: każde dołączenie nowej osoby otwiera nową rundę iteracji, nie jednorazową recenzję. Runda trwa, aż nikt nie ma więcej uwag; wtedy albo dołącza kolejna osoba i iteracja zaczyna się od nowa, albo etap jest zamknięty. Właściciel etapu (professor dla hipotezy, laborant dla eksperymentu, programmer dla implementacji) ma głos decydujący, gdy uwagi nie prowadzą do zgody.

Pokój nie ma formalnego zamknięcia — po prostu przestaje być używany, gdy praca schodzi do fazy solo albo przechodzi do kolejnego etapu (zwykłe zadanie, patrz „Zadanie czy dyskusja" niżej). Historia zostaje jako trwały zapis.

## Czekanie zamiast odpytywania

Zamiast odpytywać kanał w pętli, wołający blokuje się na `wait_for_updates` z `channel: "<slug>"` — czeka na odpowiedź w konkretnym pokoju, nie na cały ruch zespołu; nadpisuje domyślne filtrowanie po kanale sesji i po `kinds`. Limit czasu nie ma górnej granicy — można czekać długo zamiast odświeżać w pętli.

Na zakończenie zleconego zadania (nie rozmowy w pokoju) nie czeka się przez `ai-crew-sync` — `orx agent spawn` sam wybudza sesję-rodzica z odpowiedzią helpera, gdy jego sesja się kończy (chyba że użyto `--no-wake`; szczegóły w natywnym skillu `/orx-agent-delegation`, patrz niżej); osobny mechanizm czekania na `task_key` w `ai-crew-sync` okazał się zbędny i został wycofany. Ta odpowiedź jest ucięta po ok. 4000 znakach — patrz "Delegowanie do innej sesji" niżej.

## Delegowanie do innej sesji

Zanim zaczniesz nową sesję, sprawdź `list_agents`: jeśli persona, której potrzebujesz, już działa (np. laborant obsługujący tę hipotezę), napisz do niej P2P (`ask_agent`) zamiast spawnować kolejną. Dopiero gdy nikt taki nie jest aktywny, użyj `orx agent spawn`.

Przed użyciem `orx agent spawn` przeczytaj natywny skill `/orx-agent-delegation` — tam jest składnia, ochrona brancha, `--no-wake`, sprzątanie (`orx agent kill`). Nie powtarzamy tego tutaj.

Jednego ten natywny skill nie wie: nowa sesja nie ma żadnej domyślnej persony. Zlecający musi ją wskazać wprost w treści zadania, inaczej helper nie będzie wiedział, kim ma być. Szablon brief-u (`--stdin` dla wieloliniowego):

```text
Jesteś <persona> dla projektu <project_id>. Przeczytaj `roles/<plik-persony>.md` i kieruj się nim.

Slug hipotezy/eksperymentu: <slug, jeśli dotyczy>
Zadanie: <konkretne, samodzielne zadanie — helper nie widzi tej rozmowy>
Oczekiwany wynik: <co i w jakiej formie oddać>
```

Odpowiedź, którą wybudzona sesja-rodzic dostaje z zamknięcia helpera, jest ucięta na ok. 4000 znakach, bez ostrzeżenia i bez łatwego sposobu odzyskania reszty. Jeśli spodziewasz się dłuższej odpowiedzi (np. syntezy z szerokiego przeglądu literatury), poinstruuj helpera w briefie: zmieść syntezę w tym limicie (najważniejsze wyżej), a jeśli się nie mieści — niech pełną wersję wyśle jako wiadomość na właściwy kanał (patrz "Próg: wiadomość czy plik" niżej), a w zamkniętej odpowiedzi zostawi tylko krótkie odesłanie tam.

Dodaj `--harness <harness> --model <model>` do `orx agent spawn`, jeśli persona docelowa ma przypisany inny model niż Twój bieżący (patrz `model-assignment.md`) — bez tego dziecko dziedziczy Twój własny harness/model, nie ten przypisany docelowej personie. Nazwa harnessu jest stała (np. `antigravity`), ale nazwa modelu na niektórych harnessach zmienia się w czasie i nie ma stałego aliasu — jeśli nie znasz aktualnej wartości, sprawdź ją narzędziem tego harnessu (np. `agy models` dla `antigravity`) zamiast zgadywać.

## Próg: wiadomość czy plik

Wiadomości mają być jak najkrótsze. Pojedyncza liczba albo jedno zdanie wniosku może zostać wprost w wiadomości (albo w opisie). Wszystko dłuższe — log, diff, tabela, pełny wynik — idzie do pliku/attachmentu, a wiadomość niesie tylko ścieżkę.

Nie ma wymuszonego formatu wiadomości ani znaczników intencji — zwykły, swobodny, krótki tekst.

## Zadanie czy dyskusja

Konkretna robota prowadząca do postępu węzła (implementacja, uruchomienie, analiza) to zadanie w kolejce `ai-crew-sync` (`create_task`/`claim_task`) — ma właściciela, może mieć `depends_on`. Krytyka, pytania i propozycje to luźna dyskusja w kanale — nikt jej nie "claimuje", nikt nie jest za nią formalnie odpowiedzialny. Jedynymi stałymi elementami są hipoteza i eksperyment same w sobie, nie role wokół nich.

`create_task`/`claim_task` i `orx agent spawn` to dwa niepowiązane w `orx` mechanizmy — żaden nie wie o drugim. Użyj `create_task`, gdy zadanie trafia do wspólnej puli, którą może odebrać którykolwiek z kilku już aktywnych, równoważnych agentów (patrz `list_agents`) — wtedy oni sami je `claim_task`/`claim_next_task`-ują. Gdy zamiast tego spawnujesz dedykowanego pomocnika do jednej konkretnej roboty (patrz niżej), sam brief ze spawnu wystarcza za zadanie — nie zakładaj do niego dodatkowo formalnego `create_task`.

## Kto edytuje węzeł

`description` węzła edytuje agent aktualnie odpowiedzialny za niego na danym poziomie — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz. Pozostali wysyłają uwagi przez kanał; właściciel włącza je do opisu na bieżąco, w trakcie rozmowy (patrz "Opis węzła jako źródło prawdy" wyżej).

## Notatki

`ai-crew-sync` notes (`scope`/`key`, pełnotekstowe wyszukiwanie) są dla treści nieprzypisanej do jednego węzła — np. przekrojowe decyzje projektu. Nie kopiuj tam treści, która już ma dom w `description` konkretnego węzła. Wyjątkiem jest spis literatury (`literature/index.md`) — to zwykły plik chroniony lockiem `ai-crew-sync`, nie note (patrz `roles/librarian.md`).
