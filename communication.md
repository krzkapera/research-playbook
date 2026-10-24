# Wspólna komunikacja

## Pojęcia

- **Bus** — `ai-crew-sync`: kanały, wiadomości i P2P, obsługiwane narzędziami MCP własnej sesji (tożsamość: `agent-start.md`, krok 1).
- **Kanał hipotezy** — kanał nazwany slugiem hipotezy; eksperymenty-dzieci nie mają osobnych kanałów. Służy do treści hipotezy i zweryfikowanych wniosków naukowych; komunikacja implementacyjna laborant–koder odbywa się prywatnie P2P.
- **Dołączenie do kanału** — odczyt historii kanału (`read_messages`, `scope: "<slug>"`, `only_new: false`) i dalsza praca na nim. Zaproszenie = nazwa kanału w briefie spawnu albo we wpisie.
- **Wpis** — wiadomość na kanale (`post_message`, `channel: "<slug>"`). Każdy wpis zaczyna się etykietą roli: `[professor]`, `[laborant]`, `[programmer]`.
- **P2P** — wiadomość bezpośrednia między sesjami, wysyłana przez `post_message` (sekcje P2P i Typy wiadomości).
- **Adres P2P** — `<agent>/<session>` z wyniku `whoami` sesji (`agent-start.md`, krok 1).
- **Oddanie** — wpis albo wiadomość P2P z materiałem, na który czeka inna rola. Zawartość oddania definiuje plik roli oddającej (sekcja Co oddajesz).
- **`description`** — pole węzła w `orx`; źródło prawdy o stanie i decyzjach węzła.
- **Koder** — sesja roli `programmer` przypisana do hipotezy; ta sama sesja obsługuje implementację i wykonanie eksperymentu.
- **Właściciel etapu** — `professor` odpowiada za treść i stan hipotezy, `laborant` za protokół i weryfikację eksperymentu; szczegóły zapisu określają role i protokół locka poniżej.
- **Problem z flow** — sytuacja z listy w sekcji Problem z flow.

## Kanały

- Kanał hipotezy zakłada profesor po `orx create-experiment`: nazwa kanału = slug wypisany przez tę komendę (linia `slug:`). Po założeniu dołącza do kanału i pisze pierwszy wpis.
- Dołączasz do kanałów wskazanych w briefie i do kanału hipotezy, w której masz aktywną rolę (koder nie czyta kanału).
- Draft solo nie wymaga innych na kanale. Zaproszenie innych otwiera wspólną dyskusję o hipotezie lub eksperymencie.

## P2P

Kanał i P2P pełnią różne funkcje: kanał archiwizuje dyskusję, a bezpośrednie P2P dostarcza wiadomość i może wznowić sesję, która zakończyła turę. Do adresowania używaj dokładnego `<agent>/<session>` z `whoami`; identyfikator sesji ORX nie jest adresem P2P. Każdą wiadomość, na którą odbiorca ma zareagować po zakończeniu swojej tury, wyślij P2P. Sam wpis na kanale nie jest sygnałem wznowienia.

- Pytania, odpowiedzi, decyzje i oddania wymagające działania adresata wysyłaj przez `post_message` z `to: "<agent>/<session>"`; dołącz `reply_to`, gdy odpowiadasz na konkretną wiadomość. Komunikacja implementacyjna laborant–koder idzie wyłącznie P2P: nie kopiuj jej ani technicznych podsumowań na kanał czytany przez profesora. Kanał służy treści hipotezy i naukowemu raportowi laboranta.
- Nie używaj `ask_agent` jako blokującego oczekiwania ani nie uruchamiaj `wait_for_updates` w pętli. Po wysłaniu pytania lub oddania, gdy nie masz innej pracy do wykonania, zakończ turę. Po wznowieniu odczytaj nowe P2P i kontynuuj.
- Odpowiadaj na każdą wiadomość wymagającą działania. Nie wysyłaj osobnego ACK, jeśli sama odpowiedź albo wykonanie zlecenia potwierdza odbiór — unikaj pętli niepotrzebnych wybudzeń.
- P2P nie przerywa trwającej tury. Gdy pracujesz dłużej, odczytuj nowe wiadomości (`read_messages`, `scope: "all"`, `only_new: true`) w punktach kontrolnych: po zakończeniu bieżącej czynności, a przed rozpoczęciem następnej — zwłaszcza po utworzeniu węzła, zapisie `description`, wpisie na kanale, wysłaniu P2P i sprawdzeniu stanu joba. Wiadomość pilną (blokuje czyjąś pracę albo zagraża poprawności badania) obsłuż w najbliższym punkcie kontrolnym; niepilną — po domknięciu bieżącego etapu.

## Sprawdzanie limitu adresata

Przed wysłaniem P2P, na które adresat ma zareagować, sprawdź jego limit komendą `limits` (bez argumentów). Harness adresata wynika z `<agent>` w jego adresie P2P (`agent-start.md`, tabela harnessów). `limits` wypisuje sekcje `# Claude`, `# Antigravity`, `# Cursor` i `# Codex`; OpenCode nie występuje. Jeśli `limits` zwraca błąd dla harnessu, nie zakładaj, że limit tego harnessu został sprawdzony. Harness albo model adresata jest niedostępny, gdy jego limit jest wyczerpany:

- Claude: `Current session` albo `Current week` — 100% used;
- Antigravity: `Remaining` 0% albo `disabled` w którymkolwiek oknie grupy modelu (`Gemini Models` dla Gemini, `Claude and GPT models` dla Sonnet 4.6). Grupa ma dwa okna, `Five Hour Limit Remaining` i `Weekly Limit Remaining`; jej zapas to mniejsza z tych dwóch wartości, nie sama wartość tygodniowa;
- Cursor: ignoruj `totalPercentUsed` i `message: You've hit your usage limit`. Decydują dwie linie: `autoPercentUsed` = limit Groka (`grok-4.7-medium`), `apiPercentUsed` = limit GPT (`gpt-5.6-sol-medium`). Model jest niedostępny dopiero przy 100% w swojej linii;
- Codex: `primary` albo `secondary` — 100% used;
- OpenCode: brak w `limits`, limit nieznany i wspólny dla wszystkich sesji OpenCode; jest wyczerpany, gdy tura sesji OpenCode kończy się komunikatem „Free usage exceeded”.

Adresat z wyczerpanym limitem nie odpowie do jego odnowienia; nie zakładaj szybkiej odpowiedzi. Gdy ostatnia tura adresata w `orx` zakończyła się na limicie użycia i nie została wznowiona, `ra-agentd` od razu odpowiada nadawcy P2P: „adresat niedostępny na czas nieokreślony.” Dotyczy to harnessów codex, claude-code, cursor i opencode, nie antigravity — dla adresata na antigravity sprawdzasz limit wyłącznie przez `limits`.

## Typy wiadomości

Wiadomości poniżej są typami treści istniejących wiadomości P2P (`post_message`), a nie nowymi narzędziami. Każda wiadomość operacyjna zawiera `request_id` (gdy dotyczy zlecenia), `project_id`, `node_id` (jeśli dotyczy węzła), rolę nadawcy i odbiorcę.

| Typ | Nadawca → odbiorca | Znaczenie |
|---|---|---|
| `QUESTION` / `ANSWER` | laborant ↔ professor | Tylko treść, zakres lub interpretacja hipotezy; wiadomość wskazuje pytanie, na które odpowiada. |
| `HYPOTHESIS_APPROVED` | professor → laborant (P2P) | Przejście `ROBOCZA` → `GOTOWA DO IMPLEMENTACJI`: professor najpierw zapisuje stan w `description`; laborant kontynuuje po otrzymaniu komunikatu. |
| `IMPLEMENTATION_REQUEST` | laborant → przypisany programmer (P2P) | Zlecenie kolejnego testu (`NEXT_TEST`) z nowego briefu kodera; wiadomość podaje jego ścieżkę. Pierwszym zleceniem jest brief ze spawnu. |
| `PLAN_QUESTION` / `PLAN_ANSWER` | programmer ↔ laborant (P2P) | Krytyczne uwagi i dopracowanie planu przed implementacją; laborant aktualizuje brief. |
| `IMPLEMENTATION_QUESTION` / `IMPLEMENTATION_ANSWER` | programmer ↔ laborant (P2P) | Pytania i decyzje, które pojawiają się w trakcie implementacji lub wykonania eksperymentu. Zmiana technicznego planu trafia do briefu; zmiana pytania badawczego lub protokołu eksperymentu do `description`. |
| `CODE_REVIEW_REQUEST` / `CODE_REVIEW_ANSWER` | programmer ↔ laborant (P2P) | Po udanym smoke, przed pełnym jobem: laborant sprawdza kluczowe miejsca w kodzie i zgodność z planem; odpowiedź to akceptacja albo konkretne poprawki. Pełny job startuje po akceptacji. |
| `RESULTS_READY` | programmer → laborant (P2P) | Wyniki techniczne i artefakty gotowe do merytorycznej weryfikacji; bez professora. |
| `REWORK_REQUEST` | laborant → programmer (P2P) | Konkretna brakująca kontrola lub poprawka planu/kodu/wyników. |
| `RESEARCH_REPORT` | laborant → professor (P2P i kanał hipotezy) | Zweryfikowany wniosek naukowy, istotne metryki, ograniczenia i pytanie dalsze; bez kodu, logów ani szczegółów implementacji. |
| `LITERATURE_REPORT` | professor → laborant (P2P i kanał hipotezy) | Synteza przeglądu literatury, o który laborant poprosił P2P z zakresem; wskazanie źródeł. |
| `NEXT_TEST` | professor → laborant (P2P) | Kolejny test w hipotezie `GOTOWA DO IMPLEMENTACJI`; laborant tworzy eksperyment jako bezpośrednie dziecko hipotezy i zleca go przez P2P koderowi już przypisanemu do tej hipotezy. |
| `FLOW_BLOCKED` | dowolna sesja → nadawca zlecenia | Blokada techniczna, środowiskowa, limitu, RAM lub wybudzenia. Nadawca kończy turę. |
| `LOCK_RETRY_REQUEST` | autor opisu → aktualny właściciel locka | Prośba o krótkie powiadomienie po zwolnieniu locka; nie przyznaje prawa do zapisu. |
| `LOCK_RELEASED` | dotychczasowy właściciel → oczekujący autor | Informacja o zwolnieniu; odbiorca musi ponownie zdobyć lock i świeżo odczytać description. |

## Opis węzła vs wpis

`description` jest źródłem prawdy o stanie naukowym węzła: twierdzeniu hipotezy profesora, uzgodnionym protokole eksperymentu laboranta oraz zweryfikowanych ustaleniach naukowych. Każda rola zapisuje w nim wyłącznie ustalenia należące do niej według instrukcji roli. Szczegółowy plan implementacji znajduje się w briefie kodera w artifacts, nie w `description`. Nie kopiuj tam prywatnej korespondencji, logów ani roboczych szczegółów implementacji. Każda zmiana odczytuje aktualny pełny opis i zachowuje treść wpisaną przez innych. Ponieważ `orx exp desc --set/--stdin` nadpisuje całość, wszystkich edytorów obowiązuje wspólny lock: `orx-desc:<project_id>:<node_id>`. Po `acquire_lock` z TTL 300 s odczytaj aktualny opis, wprowadź swoją zmianę, zapisz całość przed wygaśnięciem i zwolnij lock. Jeśli locka nie uzyskasz, nie zapisuj; jeśli dzierżawa wygasła lub własność jest niepewna, odrzuć kopię i ponownie odczytaj po zdobyciu locka. Nie czekaj na innych ani nie kończ tury, trzymając lock. Po zwolnieniu locka odpowiedz `LOCK_RELEASED` oczekującym, którzy wysłali `LOCK_RETRY_REQUEST`; ta wiadomość nie przyznaje prawa do zapisu. Blokada jest kooperacyjna — ORX nie wymusza jej przy zapisie.

Wpis to krótka delta, a historia kanału jest archiwum dyskusji. Wpis nie zastępuje aktualizacji naukowego `description` przez właściwego autora.

## Koniec tury zamiast czekania

Jeśli dalszy postęp zależy od wiadomości innej osoby, wyślij jej P2P z konkretnym pytaniem lub oddaniem. Dyskusję o hipotezie i wnioski naukowe archiwizuj na kanale; implementacyjne wiadomości pozostają wyłącznie P2P. Gdy nie masz innej pracy, zakończ turę — mostek wznowi sesję po nowej wiadomości P2P. Po wznowieniu odczytaj nowe P2P (`read_messages`, `scope: "all"`, `only_new: true`) i ponownie sprawdź `description` lub status runu. Nie używaj `wait_for_updates`, blokującego `ask_agent` ani pętli `sleep` do czekania na zdarzenia. Jedynym wyjątkiem jest sprawdzanie startu joba przez kodera (`roles/programmer.md` § Po zgłoszeniu joba).

Przy `acquired: false` nie zapisuj: możesz wykonywać inną pracę albo wysłać właścicielowi `LOCK_RETRY_REQUEST`, zakończyć turę i wrócić po `LOCK_RELEASED`; przed zapisem ponownie zdobądź lock i odczytaj aktualny opis. Sam upływ TTL ani awaria właściciela nie budzą sesji — w takim przypadku zakończ turę.

Stan pracy innych agentów odczytujesz z kanałów i P2P; `list_agents` i presence nie dowodzą, czy agent pracuje. Turę kończysz po oddaniu, gdy nie masz dalszej pracy, albo po Problemie z flow.

## Wznowienie

Wiadomość użytkownika bez nowego zadania (np. „kontynuuj”) wznawia przerwaną pracę:

1. `read_messages` z `scope: "all"` i `only_new: true`: nowe wpisy na Twoich kanałach i wiadomości P2P od ostatniego odczytu.
2. `orx exp desc <id>` i `orx exp status <id>` węzła z briefu.
3. Ustalasz ostatni wykonany krok flow z pliku roli i kontynuujesz od następnego; oczekujące pytanie P2P albo wpis skierowany do Ciebie obsługujesz najpierw.

Wiadomość użytkownika „playbook zaktualizowany” → pliki playbooka czytasz ponownie z `~/playbook/` (`agent-start.md` § Wersja playbooka) i dalej stosujesz nową wersję; potem jak przy „kontynuuj”.

Odpowiedź kończąca turę nie budzi nikogo. Oddanie wymagające działania wysyłaj zleceniodawcy bezpośrednio P2P; wpis na kanale może służyć jako archiwum, ale nie zastępuje P2P.

## Problem z flow

Problem z flow to:

- błąd busa: MCP `ai-crew-sync` się nie ładuje albo narzędzie zwraca błąd przy odczycie lub wysyłaniu wiadomości P2P;
- błąd środowiska: narzędzie, komenda (`orx`, `git`, `ssh`) albo usługa zwraca błąd uwierzytelnienia, uprawnień, konfiguracji albo niedostępności;
- wynik `whoami` niezgodny z `agent-start.md` (krok 1);
- nieudany spawn: `orx agent spawn` nie wypisuje `Spawned agent session …`.

Działanie:

1. Gdy bus działa i `whoami` jest poprawne, wyślij `FLOW_BLOCKED` P2P do nadawcy zlecenia. Nie publikuj problemów technicznych na kanale hipotezy; kanał może zawierać tylko istotny dla hipotezy skutek naukowy.
2. To samo w odpowiedzi kończącej turę.
3. Koniec tury.

Konfiguracja środowiska i projektu jest tylko do odczytu: konfiguracje harnessu i MCP, tokeny, pliki env, usługi, bus, ustawienia `orx`, konfiguracja i hooki git repozytorium projektu. Błąd busa albo środowiska obsługujesz wyłącznie krokami Działania.

Decyzję należącą do innej roli podejmuje wyłącznie ta rola.

## Spawn i cleanup

Nie uruchamiaj ani nie usuwaj sesji samodzielnie (`orx agent spawn`, `orx agent kill`).

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
