# Wspólne identyfikatory

## id vs slug

- Komendy `orx` (`exp status` / `desc` / `run` / `cancel` / `wake` / `wait`, `--parent`) biorą wewnętrzne **`id` węzła**, nie slug.
- **Slug** (np. `lora-rank-vs-shots`) to czytelna nazwa: `orx` generuje ją z `--title` przy tworzeniu węzła; staje się branchą `orx/<slug>` i nazwą kanału. Nie ma osobnej flagi na slug.

Po utworzeniu węzła zapisz wypisane `id`. Gdy go nie masz: `orx project view <project_id>` (lista: `id`, tytuł, branch).

## project_id

Cała praca badawcza siedzi w jednym projekcie `orx`. `project_id` jest stały.

`project_id`: `<nieustawiony — uzupełnia użytkownik po utworzeniu projektu; komenda: orx projects>`

- Projekt `orx` zakłada użytkownik.
- Nie wymyślaj `project_id`.
- Brak w tym pliku i w zleceniu → `orx projects` albo pytanie na kanale `project` / do nadawcy.
- Gdy użytkownik wpisze tu wartość — to źródło prawdy.

## Nowe węzły i runy

- Slug zostaje przy węźle na stałe. Nowy wariant pytania albo inna logika porównania = **nowy węzeł** (nowy slug), zwykle dziecko istniejącego.
- Pojedyncze uruchomienie = run `orx`: `orx runs <project_id> [--experiment <id>]`, logi: `orx logs <run_id>`.
