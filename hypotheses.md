# Hipotezy badawcze

Hipoteza to węzeł drzewa `orx` bez własnego runu — korzeń dla eksperymentów, które ją testują. Komendy `orx` biorą wewnętrzne `id` węzła, nie slug (patrz `identifiers.md`).

## Tworzenie

- Pierwsza hipoteza w projekcie: `orx create-experiment <project_id> --title "..."` (bez dodatkowych flag).
- Każda kolejna **niezależna** hipoteza (nowy korzeń, nie dziecko): jawne `--baseline`. Bez tej flagi, gdy korzeń już istnieje, nowy węzeł trafi pod najstarszy korzeń zamiast stać się osobnym korzeniem.
- Slug (np. `lora-rank-vs-shots`) powstaje z tytułu: branch, kanał, nazwa robocza.

## description

Treść hipotezy (twierdzenie, narracja, stan, uzasadnienie) żyje w `description` (`orx exp desc`). Pole jest **nadpisywane w całości**: przed zapisem odczytaj bieżącą treść (`orx exp status` / `orx exp desc`) i zapisz pełną zaktualizowaną wersję.

Przy zmianie stanu dopisz krótkie uzasadnienie i wskazanie dowodów. Opis ma być samowystarczalny dla kogoś, kto nie czytał kanału.

## Kanał

Każda aktywna hipoteza ma kanał `ai-crew-sync` nazwany jej slugiem. Zakłada go `professor` przy tworzeniu węzła, zaraz po utworzeniu, i ogłasza to na kanale `project`. Ustalenia trwałe wracają do `description`; kanał to historia dyskusji (`communication.md`).

## Równoległość

Różne hipotezy i eksperymenty-dzieci mogą iść równolegle (`orx project view <project_id>`).
