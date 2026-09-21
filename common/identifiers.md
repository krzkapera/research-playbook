# Wspólne identyfikatory

`orx` nadaje każdemu węzłowi własny, wewnętrzny `id` — to właśnie `id`, nie slug, przyjmują `orx exp status/desc/run/cancel/wake/wait` oraz `--parent`. Slug (np. `lora-rank-vs-shots`) jest naszą czytelną nazwą: `orx` generuje go sam z `--title` przy tworzeniu (`orx create-experiment <project_id> --title "..."` — nie ma osobnej flagi na slug), staje się nazwą brancha (`orx/<slug>`) i kanału `ai-crew-sync` — ale to nie jest to samo, co przyjmują komendy `orx`.

Zaraz po utworzeniu węzła zapisz jego `id` (komenda go wypisuje) — np. jako pierwszą linię `description` albo w metadanych zadania, którego dotyczy. Jeśli go zabraknie, znajdź go przez `orx project view <project_id>`: lista pokazuje `id`, tytuł i branch `orx/<slug>` każdego węzła.

Cały projekt badawczy żyje w jednym `orx` projekcie; jego `project_id` jest stały na czas całej pracy — wpisz go tutaj, gdy projekt powstanie (`orx create-experiment`/`orx projects`): `<project_id: do uzupełnienia>`.

Slug nadaje się raz i nie zmienia po ponowieniu eksperymentu. Nowy wariant tego samego pytania to nowy węzeł-dziecko z osobnym slugiem (np. `lora-rank-vs-shots-v2`); zmiana pytania lub logiki porównania też tworzy nowy węzeł, nie nadpisuje istniejącego.

`orx` sam pilnuje unikalności slugu i brancha w obrębie projektu — nie potrzeba osobnego locka na przydział numeru.

Pojedyncze uruchomienie identyfikujemy runem `orx` (`orx runs <project_id> [--experiment <id>]`, `orx logs <run_id>`), nie osobnym schematem numeracji jobów.
