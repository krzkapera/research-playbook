# Wspólna komunikacja

Każdy agent dołącza do `project`. Każda aktywna hipoteza i każdy aktywny eksperyment ma kanał nazwany swoim slugiem z `orx` — to pokój do rozmowy o tym węźle, nie magazyn stanu (patrz "Pokój" niżej). P2P służy sprawom skierowanym do konkretnego agenta. `ai-crew-sync` zapewnia wiadomości, delegowanie (`ask_agent`), kanały, zadania, locki i blokujące czekanie (`wait_for_updates`).

## Opis węzła jako źródło prawdy

`description` węzła jest samowystarczalną dokumentacją — ma wystarczać do zrozumienia stanu i decyzji nawet komuś, kto nie widział rozmowy: nowemu uczestnikowi, ale też właścicielowi wracającemu do tematu po przerwie albo prowadzącemu wiele równoległych wątków naraz (np. professor). Aktualizuje się go na bieżąco, w trakcie rozmowy w pokoju, a nie dopiero na koniec — bo ta rozmowa istnieje właśnie po to, żeby dopracować ten opis (patrz "Kto edytuje węzeł" niżej).

Skoro opis jest zawsze aktualny, wiadomość w pokoju nie powtarza tego, co w nim już jest. Wiadomość to krótka delta: co się zmieniło w opisie i o co się pyta — nie treść merytoryczna od nowa. Historia kanału jest archiwum "dlaczego tak zdecydowano", do którego sięga się na żądanie, nigdy wymaganą lekturą.

## Pokój: kiedy się otwiera i jak długo trwa

Pokój (kanał) otwiera się dopiero, gdy jest gotowy konkretny draft do recenzji — nie na starcie pracy nad czymś nowym. Właściciel etapu pracuje najpierw sam, zapisuje wynik w opisie węzła, dopiero potem zaprasza kolejnych uczestników wiadomością wskazującą kanał — `ai-crew-sync` nie ma ACL na kanały, więc "zaproszenie" to po prostu przekazanie nazwy.

Skład pokoju rośnie stopniowo, nie od razu w komplecie: każde dołączenie nowej osoby otwiera nową rundę iteracji, nie jednorazową recenzję. Runda trwa, aż nikt nie ma więcej uwag; wtedy albo dołącza kolejna osoba i iteracja zaczyna się od nowa, albo etap jest zamknięty. Właściciel etapu (professor dla hipotezy, laborant dla eksperymentu, implementer dla implementacji) ma głos decydujący, gdy uwagi nie prowadzą do zgody.

Pokój nie ma formalnego zamknięcia — po prostu przestaje być używany, gdy praca schodzi do fazy solo albo przechodzi do kolejnego etapu (zwykłe zadanie, patrz "Zadanie czy dyskusja" niżej). Historia zostaje jako trwały zapis.

## Czekanie zamiast odpytywania

Zamiast odpytywać kanał w pętli, wołający blokuje się na `wait_for_updates` z `channel: "<slug>"` — czeka na odpowiedź w konkretnym pokoju, nie na cały ruch zespołu; nadpisuje domyślne filtrowanie po kanale sesji i po `kinds`. Limit czasu nie ma górnej granicy — można czekać długo zamiast odświeżać w pętli.

Na zakończenie zleconego zadania (nie rozmowy w pokoju) nie czeka się przez `ai-crew-sync` — `orx agent spawn` sam wybudza sesję-rodzica, gdy sesja zaspawnowanego dziecka się kończy (patrz `worktrees.md`); osobny mechanizm czekania na `task_key` w `ai-crew-sync` okazał się zbędny i został wycofany.

## Próg: wiadomość czy plik

Wiadomości mają być jak najkrótsze. Pojedyncza liczba albo jedno zdanie wniosku może zostać wprost w wiadomości (albo w opisie). Wszystko dłuższe — log, diff, tabela, pełny wynik — idzie do pliku/attachmentu, a wiadomość niesie tylko ścieżkę.

Nie ma wymuszonego formatu wiadomości ani znaczników intencji — zwykły, swobodny, krótki tekst.

## Zadanie czy dyskusja

Konkretna robota prowadząca do postępu węzła (implementacja, uruchomienie, analiza) to zadanie w kolejce `ai-crew-sync` — ma właściciela, może mieć `depends_on`. Krytyka, pytania i propozycje to luźna dyskusja w kanale — nikt jej nie "claimuje", nikt nie jest za nią formalnie odpowiedzialny (patrz `coordination-flow.md`). Jedynymi stałymi elementami są hipoteza i eksperyment same w sobie, nie role wokół nich.

## Kto edytuje węzeł

`description` węzła edytuje agent aktualnie odpowiedzialny za niego na danym poziomie (patrz `file-lifecycle.md`) — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz. Pozostali wysyłają uwagi przez kanał; właściciel włącza je do opisu na bieżąco, w trakcie rozmowy (patrz "Opis węzła jako źródło prawdy" wyżej).

## Notatki

`ai-crew-sync` notes (`scope`/`key`, pełnotekstowe wyszukiwanie) są dla treści nieprzypisanej do jednego węzła — np. stały indeks literatury od librariana, przekrojowe decyzje projektu. Nie kopiuj tam treści, która już ma dom w `description` konkretnego węzła.
