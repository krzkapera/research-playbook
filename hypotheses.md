# Hipotezy badawcze

Hipoteza to węzeł drzewa `orx` bez własnego runu — korzeń dla eksperymentów, które ją testują. Pierwszą w projekcie tworzy się przez `orx create-experiment <project_id> --title "..."` bez dodatkowych flag. **Każda kolejna, niezależna hipoteza (nowy korzeń, nie dziecko istniejącej) wymaga jawnego `--baseline`** — sama komenda bez flag, gdy korzeń już istnieje, dołączy nowy węzeł pod najstarszym istniejącym korzeniem zamiast utworzyć nowy. Węzeł dostaje krótki, czytelny slug (np. `lora-rank-vs-shots`) jako naszą nazwę robocza (branch, kanał), ale komendy `orx` operują na jej wewnętrznym `id`, nie na slugu — patrz `common/identifiers.md`.

Treść hipotezy — samo twierdzenie, jej narracja i stan — żyje w polu `description` tego węzła, edytowanym przez `orx exp desc`. To pole jest nadpisywane w całości przy każdej zmianie: przed edycją odczytaj bieżącą treść (`orx exp status`/`orx exp desc`) i zapisz pełną, zaktualizowaną wersję, nie tylko dopisek.

## Zawartość opisu

Dobierz treść opisu tak, żeby był samowystarczalny (patrz `common/communication.md`). Hipoteza może być szkicem, który rozmowa dopiero doprecyzuje.

## Stan hipotezy

Bieżący stan i uzasadnienie opisz swobodnym tekstem w `description`, tak jak akurat pasuje do sytuacji. Przy zmianie stanu dopisz krótkie uzasadnienie i wskazanie dowodów.

## Równoległość

Hipotezy tworzą drzewo: `orx project view <project_id>` pokazuje wszystkie naraz, wraz z eksperymentami-dziećmi. Równolegle rozwijaj różne gałęzie oraz eksperymenty tej samej hipotezy, gdy to ma sens; każdy eksperyment może mieć własny kształt i tempo.

## Kanał

Każda aktywna hipoteza ma kanał `ai-crew-sync` nazwany jej slugiem. **Zakłada go agent tworzący węzeł (zazwyczaj `professor`)**, zaraz po `orx create-experiment`, i ogłasza powstanie na kanale `project`. Kanał nie zastępuje `description`: ustalenia trwałe wracają do węzła, kanał jest historią dyskusji. Szczegóły dołączania: `common/communication.md`.

