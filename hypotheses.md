# Hipotezy badawcze

Hipoteza to węzeł drzewa `orx` bez własnego runu — korzeń dla eksperymentów, które ją testują. Tworzy się ją przez `orx create-experiment <project_id> --title "..."`; dostaje krótki, czytelny slug (np. `lora-rank-vs-shots`) jako naszą nazwę robocza (branch, kanał), ale komendy `orx` operują na jej wewnętrznym `id`, nie na slugu — patrz `common/identifiers.md`.

Treść hipotezy — samo twierdzenie, jej narracja i stan — żyje w polu `description` tego węzła, edytowanym przez `orx exp desc`. To pole jest nadpisywane w całości przy każdej zmianie: przed edycją odczytaj bieżącą treść (`orx exp status`/`orx exp desc`) i zapisz pełną, zaktualizowaną wersję, nie tylko dopisek.

## Minimalna zawartość opisu

- jednoznaczne twierdzenie;
- motywacja i podstawy teoretyczne;
- hipoteza alternatywna;
- aktualne argumenty za i przeciw;
- otwarte pytania;
- bieżący stan i uzasadnienie;
- ostatnie decyzje oraz następny mały krok.

Nie każdy wpis musi od razu zawierać pełny plan. Hipoteza może być szkicem, który rozmowa dopiero doprecyzuje.

## Stany hipotezy

```text
PROPOSED -> DISCUSSING -> REFINED -> TESTING
TESTING -> SUPPORTED | WEAKENED | REJECTED | INCONCLUSIVE
SUPPORTED -> REFINED | MERGED | ABANDONED
WEAKENED -> REFINED | REJECTED | ABANDONED
```

Stan to linia w `description`, nie osobne pole w `orx`. Opisuje aktualny poziom uzasadnienia, nie prawdę absolutną. Zmiana stanu wymaga krótkiego uzasadnienia i wskazania dowodów — dopisz je do opisu.

## Równoległość

Hipotezy tworzą drzewo: `orx project view <project_id>` pokazuje wszystkie naraz, wraz z eksperymentami-dziećmi. Możemy równolegle rozwijać różne gałęzie oraz eksperymenty tej samej hipotezy. Nie zakładamy, że eksperymenty muszą mieć identyczny formularz ani wspólny harmonogram.

## Kanał

Każda aktywna hipoteza dostaje kanał w `ai-crew-sync` nazwany jej slugiem, do rozmowy i krytyki — patrz `common/communication.md`. Kanał nie zastępuje `description`: ustalenia trwałe wracają do węzła, kanał jest historią dyskusji.
