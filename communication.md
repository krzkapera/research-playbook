# Wspólna komunikacja

## Pojęcia

- **Bus** — `ai-crew-sync`: kanały, wiadomości i P2P, obsługiwane narzędziami MCP własnej sesji (tożsamość: `agent-start.md`, krok 1).
- **Kanał węzła** — kanał nazwany slugiem hipotezy albo eksperymentu. Służy do treści hipotezy i zweryfikowanych wniosków naukowych; komunikacja implementacyjna laborant–koder odbywa się prywatnie P2P.
- **Dołączenie do kanału** — odczyt historii kanału (`read_messages`, `scope: "<slug>"`, `only_new: false`) i dalsza praca na nim. Zaproszenie = nazwa kanału w briefie spawnu albo we wpisie.
- **Wpis** — wiadomość na kanale (`post_message`, `channel: "<slug>"`). Każdy wpis zaczyna się etykietą roli: `[orchestrator]`, `[professor]`, `[laborant]`, `[programmer]`, `[librarian]`.
- **P2P** — wiadomość bezpośrednia między sesjami, wysyłana przez `post_message` (sekcje P2P i Orchestracja).
- **Adres P2P** — `<agent>/<session>` z wyniku `whoami` sesji (`agent-start.md`, krok 1).
- **Oddanie** — wpis albo wiadomość P2P z materiałem, na który czeka inna rola. Zawartość oddania definiuje plik roli oddającej (sekcja Co oddajesz).
- **`description`** — pole węzła w `orx`; źródło prawdy o stanie i decyzjach węzła.
- **Koder** — sesja roli `programmer` przypisana przez orchestratora do hipotezy; ta sama sesja obsługuje implementację i wykonanie eksperymentu.
- **Właściciel etapu** — `professor` odpowiada za treść i stan hipotezy, `laborant` za protokół i weryfikację eksperymentu; szczegóły zapisu określają role i protokół locka poniżej.
- **Problem z flow** — sytuacja z listy w sekcji Problem z flow.

## Kanały

- Kanał węzła zakłada twórca węzła po `orx create-experiment`: nazwa kanału = slug wypisany przez tę komendę (linia `slug:`). Po założeniu twórca dołącza do kanału i pisze pierwszy wpis.
- Dołączasz do kanałów wskazanych w briefie i do kanału węzła, w którym masz aktywną rolę.
- Draft solo nie wymaga innych na kanale. Zaproszenie innych otwiera wspólną dyskusję o hipotezie lub eksperymencie.

## P2P

Kanał i P2P pełnią różne funkcje: kanał archiwizuje dyskusję, a bezpośrednie P2P dostarcza wiadomość i może wznowić sesję, która zakończyła turę. Do adresowania używaj dokładnego `<agent>/<session>` z `whoami`; identyfikator sesji ORX nie jest adresem P2P. Każdą wiadomość, na którą odbiorca ma zareagować po zakończeniu swojej tury, wyślij P2P. Sam wpis na kanale nie jest sygnałem wznowienia.

- Pytania, odpowiedzi, decyzje i oddania wymagające działania adresata wysyłaj przez `post_message` z `to: "<agent>/<session>"`; dołącz `reply_to`, gdy odpowiadasz na konkretną wiadomość. Komunikacja implementacyjna laborant–koder idzie wyłącznie P2P: nie kopiuj jej ani technicznych podsumowań na kanał czytany przez profesora. Kanał służy treści hipotezy i naukowemu raportowi laboranta.
- Nie używaj `ask_agent` jako blokującego oczekiwania ani nie uruchamiaj `wait_for_updates` w pętli. Po wysłaniu pytania lub oddania, gdy nie masz innej pracy do wykonania, zakończ turę. Po wznowieniu odczytaj nowe P2P i kontynuuj.
- Odpowiadaj na każdą wiadomość wymagającą działania. Nie wysyłaj osobnego ACK, jeśli sama odpowiedź albo wykonanie zlecenia potwierdza odbiór — unikaj pętli niepotrzebnych wybudzeń.
- P2P nie przerywa trwającej tury. Gdy pracujesz dłużej, odczytuj nowe wiadomości (`read_messages`, `scope: "all"`, `only_new: true`) w punktach kontrolnych: po zakończeniu bieżącej czynności, a przed rozpoczęciem następnej — zwłaszcza po utworzeniu węzła, zapisie `description`, wpisie na kanale, wysłaniu P2P i sprawdzeniu stanu joba. Wiadomość pilną (blokuje czyjąś pracę albo zagraża poprawności badania) obsłuż w najbliższym punkcie kontrolnym; niepilną — po domknięciu bieżącego etapu.

## Orchestracja

Wiadomości poniżej są typami treści istniejących wiadomości P2P (`post_message`), a nie nowymi narzędziami. Każda wiadomość operacyjna zawiera `request_id` (gdy dotyczy zlecenia), `project_id`, `node_id` (jeśli dotyczy węzła), rolę nadawcy i odbiorcę. Orchestrator deduplikuje zlecenia po `request_id` w rejestrze z `roles/orchestrator.md`; samo `post_message` nie zapewnia idempotencji.

| Typ | Nadawca → odbiorca | Znaczenie |
|---|---|---|
| `REQUEST_AGENT` | professor → orchestrator | Zapisana hipoteza wymaga laboranta. Professor prowadzi sam przegląd literatury dla własnej hipotezy. |
| `REQUEST_AGENT` | laborant → orchestrator | Przy prośbie o kodera laborant podaje ścieżkę do gotowego briefu z planem implementacji; przy prośbie o librariana podaje zakres przeglądu literatury. |
| `REGISTER_SESSION` | uruchomiona sesja → orchestrator | Rola, węzeł, `request_id`, identyfikator sesji ORX i adres P2P z `whoami`. |
| `AGENT_ASSIGNED` | orchestrator → zleceniodawca | Przydział roli, identyfikator sesji ORX i adres P2P. |
| `AGENT_PENDING` | orchestrator → zleceniodawca | Spawn wstrzymany przez RAM lub limit; przyczyna i pozycja w kolejce. Zleceniodawca kończy turę; `AGENT_ASSIGNED` przyjdzie po spawnie. |
| `QUESTION` / `ANSWER` | laborant ↔ professor | Tylko treść, zakres lub interpretacja hipotezy; wiadomość wskazuje pytanie, na które odpowiada. |
| `HYPOTHESIS_APPROVED` | professor → laborant (P2P) | Przejście `ROBOCZA` → `GOTOWA DO IMPLEMENTACJI`: professor najpierw zapisuje stan w `description`; laborant kontynuuje po otrzymaniu komunikatu. |
| `IMPLEMENTATION_REQUEST` | laborant → przypisany programmer (P2P) | Zlecenie realizacji planu z briefu kodera; wiadomość podaje jego ścieżkę. |
| `PLAN_QUESTION` / `PLAN_ANSWER` | programmer ↔ laborant (P2P) | Krytyczne uwagi i dopracowanie planu przed implementacją; laborant aktualizuje brief. |
| `IMPLEMENTATION_QUESTION` / `IMPLEMENTATION_ANSWER` | programmer ↔ laborant (P2P) | Pytania i decyzje, które pojawiają się w trakcie implementacji lub wykonania eksperymentu. Zmiana technicznego planu trafia do briefu; zmiana pytania badawczego lub protokołu eksperymentu do `description`. |
| `HOME_ACCESS_REQUEST` / `HOME_ACCESS_ANSWER` | programmer ↔ orchestrator (P2P) | Prośba o zgodę użytkownika na mały job CPU na `home` (projekt, węzeł, opis obciążenia) i odpowiedź z decyzją użytkownika. |
| `AGENT_DONE` | librarian → orchestrator (P2P) | Synteza przekazana zleceniodawcy; librarian nie ma dalszej pracy. Orchestrator rozpoczyna cleanup. |
| `RESULTS_READY` | programmer → laborant (P2P) | Wyniki techniczne i artefakty gotowe do merytorycznej weryfikacji; bez professora. |
| `REWORK_REQUEST` | laborant → programmer (P2P) | Konkretna brakująca kontrola lub poprawka planu/kodu/wyników. |
| `RESEARCH_REPORT` | laborant → professor (P2P i kanał hipotezy) | Zweryfikowany wniosek naukowy, istotne metryki, ograniczenia i pytanie dalsze; bez kodu, logów ani szczegółów implementacji. |
| `LITERATURE_REPORT` | librarian → zleceniodawca (P2P i wskazany kanał) | Synteza literatury w zakresie briefu i wskazanie źródeł. |
| `NEXT_TEST` | professor → laborant (P2P) | Kolejny test w hipotezie `GOTOWA DO IMPLEMENTACJI`; laborant tworzy eksperyment jako bezpośrednie dziecko hipotezy i zleca go przez P2P koderowi już przypisanemu do tej hipotezy. Orchestrator nie uczestniczy w ponownym przydziale. |
| `HYPOTHESIS_REJECTED` | professor → orchestrator | Przejście do `ODRZUCONA`: professor zapisuje powód i prosi o sprzątnięcie sesji przypisanych do węzła, nie samego węzła. |
| `HYPOTHESIS_CLOSED` | professor → orchestrator | Po pełnym sprawdzeniu professor zapisuje wniosek i prosi o sprzątnięcie sesji, nie samego węzła. |
| `WAITING` / `ACTIVE` | laborant lub programmer → orchestrator | Stan operacyjny sesji, nie stan hipotezy; nie wymaga powiadamiania profesora. |
| `FINISH_REQUEST` / `READY_TO_DELETE` | orchestrator ↔ laborant, programmer lub librarian | Cleanup po potwierdzeniu braku aktywnego joba i trwałości wyników. |
| `FLOW_BLOCKED` | dowolna sesja → orchestrator | Blokada techniczna, środowiskowa, limitu, RAM lub wybudzenia. |
| `LOCK_RETRY_REQUEST` | autor opisu → aktualny właściciel locka | Prośba o krótkie powiadomienie po zwolnieniu locka; nie przyznaje prawa do zapisu. |
| `LOCK_RELEASED` | dotychczasowy właściciel → oczekujący autor | Informacja o zwolnieniu; odbiorca musi ponownie zdobyć lock i świeżo odczytać description. |
| `RETRY_PENDING` | orchestrator (rejestr) | Zlecenie/edycja czeka na nową próbę po konflikcie, limicie lub braku zasobu; nie jest aktywnym spawnem. |


### Asynchroniczne wznowienie

Gdy skończysz bieżącą pracę i czekasz na odpowiedź lub oddanie, wyślij wymagane P2P, a następnie zakończ turę. Skonfigurowany mostek P2P → ORX dostarcza nową wiadomość do sesji i wznawia ją; po powrocie odczytaj wiadomości i sprawdź aktualny stan przed działaniem. Kanał sam w sobie nie budzi zakończonej tury. Nie wysyłaj ACK, które nie niosą odpowiedzi ani postępu.

Przy `acquired: false` nie zapisuj. Jeśli chcesz ponowić po zwolnieniu locka, wyślij właścicielowi `LOCK_RETRY_REQUEST` i zakończ turę; po P2P `LOCK_RELEASED` wznowiona sesja ponownie zdobywa lock i świeżo odczytuje opis. Sam upływ TTL ani awaria właściciela nie wysyłają powiadomienia; oznacz sprawę jako `RETRY_PENDING` i przekaż ją orchestratorowi P2P. Nie spinuj pollingiem ani nie zakładaj, że TTL sam wznowi sesję. Nie obiecuj raportów okresowych ani automatycznych retry bez działającego źródła wznowienia.

## Opis węzła vs wpis

`description` jest źródłem prawdy o stanie naukowym węzła: twierdzeniu hipotezy profesora, uzgodnionym protokole eksperymentu laboranta oraz zweryfikowanych ustaleniach naukowych. Każda rola zapisuje wyłącznie sekcję przypisaną jej w instrukcji roli. Szczegółowy plan implementacji znajduje się w briefie kodera w artifacts, nie w `description`. Nie kopiuj tam prywatnej korespondencji, logów ani roboczych szczegółów implementacji. Każda zmiana odczytuje aktualny pełny opis i zachowuje cudze sekcje. Ponieważ `orx exp desc --set/--stdin` nadpisuje całość, wszystkich edytorów obowiązuje wspólny lock: `orx-desc:<project_id>:<node_id>`. Po `acquire_lock` z TTL 300 s odczytaj aktualny opis, zmień własną sekcję, zapisz całość przed wygaśnięciem i zwolnij lock. Jeśli locka nie uzyskasz, nie zapisuj; jeśli dzierżawa wygasła lub własność jest niepewna, odrzuć kopię i ponownie odczytaj po zdobyciu locka. Nie czekaj na innych ani nie kończ tury, trzymając lock. Po zwolnieniu locka odpowiedz `LOCK_RELEASED` oczekującym, którzy wysłali `LOCK_RETRY_REQUEST`; ta wiadomość nie przyznaje prawa do zapisu. Blokada jest kooperacyjna — ORX nie wymusza jej przy zapisie.

Wpis to krótka delta, a historia kanału jest archiwum dyskusji. Wpis nie zastępuje aktualizacji naukowego `description` przez właściwego autora.


## Koniec tury zamiast czekania

Jeśli dalszy postęp zależy od wiadomości innej osoby, wyślij jej P2P z konkretnym pytaniem lub oddaniem. Dyskusję o hipotezie i wnioski naukowe archiwizuj na kanale; implementacyjne wiadomości pozostają wyłącznie P2P. Gdy nie masz innej pracy, zakończ turę — mostek wznowi sesję po nowej wiadomości P2P. Po wznowieniu odczytaj nowe P2P (`read_messages`, `scope: "all"`, `only_new: true`) i ponownie sprawdź `description` lub status runu. Nie używaj `wait_for_updates`, blokującego `ask_agent` ani pętli `sleep` do czekania na zdarzenia.

Przy `acquired: false` nie zapisuj: możesz wykonywać inną pracę albo wysłać właścicielowi `LOCK_RETRY_REQUEST`, zakończyć turę i wrócić po `LOCK_RELEASED`; przed zapisem ponownie zdobądź lock i odczytaj aktualny opis. Sam upływ TTL ani awaria właściciela nie budzą sesji — w takim przypadku przekaż `RETRY_PENDING` orchestratorowi P2P.


Stan pracy innych agentów odczytujesz z kanałów i P2P; `list_agents` i presence nie dowodzą, czy agent pracuje. Turę kończysz po oddaniu, gdy nie masz dalszej pracy, albo po Problemie z flow.

## Wznowienie

Wiadomość użytkownika bez nowego zadania (np. „kontynuuj”) wznawia przerwaną pracę:

1. `read_messages` z `scope: "all"` i `only_new: true`: nowe wpisy na Twoich kanałach i wiadomości P2P od ostatniego odczytu.
2. `orx exp desc <id>` i `orx exp status <id>` węzła z briefu.
3. Ustalasz ostatni wykonany krok flow z pliku roli i kontynuujesz od następnego; oczekujące pytanie P2P albo wpis skierowany do Ciebie obsługujesz najpierw.

Wiadomość użytkownika „playbook zaktualizowany” → pliki playbooka czytasz ponownie z `~/playbook/` (`agent-start.md` § Wersja playbooka) i dalej stosujesz nową wersję; potem jak przy „kontynuuj”.

Odpowiedź spawnu dziecka nie jest sama w sobie sygnałem wznowienia rodzica. Oddanie wymagające działania wysyłaj rodzicowi bezpośrednio P2P; wpis na kanale może służyć jako archiwum, ale nie zastępuje P2P.

## Problem z flow

Problem z flow to:

- błąd busa: MCP `ai-crew-sync` się nie ładuje albo narzędzie zwraca błąd przy odczycie lub wysyłaniu wiadomości P2P;
- błąd środowiska: narzędzie, komenda (`orx`, `git`, `ssh`) albo usługa zwraca błąd uwierzytelnienia, uprawnień, konfiguracji albo niedostępności;
- wynik `whoami` niezgodny z `agent-start.md` (krok 1);
- nieudany spawn: `orx agent spawn` nie wypisuje `Spawned agent session …`.

Działanie:

1. Gdy bus działa i `whoami` jest poprawne, wyślij `FLOW_BLOCKED` P2P do orchestratora i nadawcy zlecenia. Nie publikuj problemów technicznych na kanale hipotezy; kanał może zawierać tylko istotny dla hipotezy skutek naukowy.
2. To samo w odpowiedzi do rodzica albo użytkownika.
3. Koniec tury.

Konfiguracja środowiska i projektu jest tylko do odczytu: konfiguracje harnessu i MCP, tokeny, pliki env, usługi, bus, ustawienia `orx`, konfiguracja i hooki git repozytorium projektu. Błąd busa albo środowiska obsługujesz wyłącznie krokami Działania.

Decyzję należącą do innej roli podejmuje wyłącznie ta rola.

## Spawn i cleanup

Wyłącznie orchestrator wykonuje `orx agent spawn` i `orx agent kill`; pozostałe role proszą go o przydział przez P2P `REQUEST_AGENT`. Nie uruchamiaj ani nie usuwaj sesji samodzielnie.

## Roundtrip (niejasny brief)

Gdy brief lub `description` nie wystarcza do kontynuacji, dopytaj nadawcę zlecenia bezpośrednio P2P. Jeśli sprawa dotyczy treści hipotezy, zwróć się do jej właściciela wskazanego w instrukcji roli.

1. Dziecko zadaje pytania tą drogą.
2. Jeśli nie ma innej pracy, dziecko kończy turę.
3. Nadawca odpowiada P2P i/lub aktualizuje `description`.
4. Wiadomość wznowi dziecko; ono odczytuje odpowiedź i kontynuuje w tej samej sesji.

## Wiadomość vs plik

Krótki wniosek mieści się we wpisie. Log, diff, tabela, wykres, długi wynik → plik w miejscu według `identifiers.md` § Miejsca zapisu; we wpisie ścieżka albo link `artifacts/research/<slug>/…`. Styl: zwykły, krótki tekst.

## Literatura

Korpus i spis literatury: `~/literature/` (`identifiers.md` § Miejsca zapisu).
