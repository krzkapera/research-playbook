# Hipotezy badawcze

Hipoteza to węzeł drzewa `orx` bez własnego runu, korzeń dla eksperymentów, które ją testują. Komendy `orx` biorą wewnętrzne `id` węzła, nie slug (`identifiers.md`).

## Tworzenie

- Pierwsza hipoteza w projekcie: `orx create-experiment <project_id> --title "..."` (bez dodatkowych flag).
- Każda kolejna **niezależna** hipoteza (nowy korzeń, nie dziecko): jawne `--baseline`. Bez tej flagi, gdy korzeń już istnieje, nowy węzeł trafia pod najstarszy korzeń.
- Komenda wypisuje `id` i slug (linia `slug:`, np. `lora-rank-vs-shots`). Slug to nazwa brancha `orx/<slug>` i kanału.

## description

Treść hipotezy (twierdzenie, narracja, stan, uzasadnienie merytoryczne) żyje w `description` (`orx exp desc`). Pole jest **nadpisywane w całości**: przed zapisem odczytaj bieżącą treść (`orx exp status` / `orx exp desc`) i zapisz pełną zaktualizowaną wersję.

Przy zmianie stanu dopisz krótkie uzasadnienie i wskazanie dowodów. Opis jest samowystarczalny dla kogoś, kto nie czytał kanału.

## Kanał

Każda aktywna hipoteza ma kanał nazwany jej slugiem. Zakłada go `professor` po utworzeniu węzła, według `communication.md` § Kanały.

## Równoległość

Różne hipotezy i eksperymenty-dzieci mogą iść równolegle (`orx project view <project_id>`).
