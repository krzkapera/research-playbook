# Hipotezy badawcze

Hipoteza to węzeł `orx` z twierdzeniem badawczym, który może być zarazem węzłem własnego eksperymentu głównego. Dodatkowe pytania badawcze mogą być eksperymentami-dziećmi. Nie twórz osobnego węzła wyłącznie dla protokołu, jeśli jest jeden eksperyment. Komendy `orx` biorą wewnętrzne `id`, nie slug (`identifiers.md`).

## Tworzenie

- Każda nowa, niezależna hipoteza (nowy korzeń, nie dziecko): `orx create-experiment <project_id> --baseline --title "..."`, także gdy projekt jest pusty. Bez `--baseline` nowy węzeł trafia pod najstarszy istniejący korzeń, co przy kilku profesorach pracujących równolegle daje błędne drzewo.
- Komenda wypisuje `id` i slug (linia `slug:`, np. `lora-rank-vs-shots`). Slug to nazwa brancha `orx/<slug>` i kanału.

## Stany i przejścia

`description` hipotezy zawiera dokładnie jedno pole `Stan:` z jedną z poniższych wartości. Professor jest właścicielem tego pola i zapisuje zmianę wraz z krótkim uzasadnieniem oraz dowodami.

| Stan | Znaczenie | Dozwolone przejście |
|---|---|---|
| `ROBOCZA` | Hipoteza jest tworzona lub dopracowywana; może obejmować uzgodnienie draftu z użytkownikiem w trybie „użytkownik jako krytyk” oraz krytykę laboranta. | `GOTOWA DO IMPLEMENTACJI` po decyzji professora; `ODRZUCONA`, jeśli professor rezygnuje z jej badania przed wykonaniem eksperymentu. |
| `GOTOWA DO IMPLEMENTACJI` | Professor zaakceptował hipotezę po jej dopracowaniu; laborant może przygotować jej realizację, a koder ją wdraża. | Pozostaje bez zmian podczas implementacji, poprawek i kolejnych testów (`NEXT_TEST`); `ODRZUCONA`, jeśli badanie zostaje przerwane przed wykonaniem eksperymentu; przechodzi do `ZAMKNIĘTA` dopiero po sprawdzeniu hipotezy i decyzji, że nie ma dalszych testów. |
| `ODRZUCONA` | Professor rezygnuje z badania hipotezy przed wykonaniem eksperymentu; zapisuje powód. Nie jest to wniosek oparty na wynikach eksperymentu. Stan terminalny. | Brak. Dalsze pytanie badawcze wymaga nowej hipotezy. |
| `ZAMKNIĘTA` | Hipoteza została sprawdzona, professor zapisał wniosek i nie planuje dalszych eksperymentów. Stan terminalny. | Brak. Dalsze pytanie badawcze wymaga nowej hipotezy. |

Przy przejściu `ROBOCZA` → `GOTOWA DO IMPLEMENTACJI` professor zapisuje stan w `description`, a następnie wysyła laborantowi P2P `HYPOTHESIS_APPROVED`. Jeśli rezygnuje z badania przed wykonaniem eksperymentu, ustawia `ODRZUCONA` i wysyła `HYPOTHESIS_REJECTED` do orchestratora, aby posprzątał przypisane sesje. Dopiero po sprawdzeniu hipotezy i decyzji o braku dalszych testów professor ustawia `ZAMKNIĘTA` i wysyła `HYPOTHESIS_CLOSED`. `NEXT_TEST` nie jest stanem ani zamknięciem — oznacza kolejny test w ramach otwartej hipotezy. Laborant tworzy go jako eksperyment-dziecko bezpośrednio pod węzłem hipotezy; nie tworzy dziecka poprzedniego eksperymentu.

Stany sesji (`WAITING`, `ACTIVE`), kolejki (`RETRY_PENDING`) i runów należą do rejestru operacyjnego lub ORX, nie do pola `Stan` hipotezy. Nie twórz osobnego stanu hipotezy dla oczekiwania na zasoby, błędu joba, poprawki kodu ani kolejnej iteracji implementacji.

## description

Treść hipotezy (twierdzenie, narracja, stan, uzasadnienie merytoryczne) żyje w `description` (`orx exp desc`). Pole jest **nadpisywane w całości**. Profesor zapisuje bezpośrednio własne wnioski i decyzje; przed zapisem stosuje kooperacyjny lock `orx-desc:<project_id>:<node_id>` z `communication.md` § Opis węzła vs wpis: acquire → świeży odczyt → zmiana z zachowaniem pozostałych sekcji → zapis → release. Nie zapisuj treści odczytanej przed uzyskaniem locka. Po utracie dzierżawy odrzuć kopię i zacznij od świeżego odczytu.

Przy zmianie stanu dopisz krótkie uzasadnienie i wskazanie dowodów. Opis jest samowystarczalny dla kogoś, kto nie czytał kanału.

## Kanał

Każda aktywna hipoteza ma kanał nazwany jej slugiem. Zakłada go `professor` po utworzeniu węzła, według `communication.md` § Kanały.

## Równoległość

Różne hipotezy i eksperymenty-dzieci mogą iść równolegle (`orx project view <project_id>`).
