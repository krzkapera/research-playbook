# Wspólne identyfikatory

Ten plik ustala nazwy projektu, węzłów i runów `orx` oraz którego identyfikatora używać w komendach.

## Pojęcia

Zanim przejdziesz dalej, te słowa oznaczają w playbooku konkretne rzeczy:

- **`project_id`** — stały identyfikator jednego projektu `orx`, w którym siedzi cała praca badawcza.
- **Węzeł** — hipoteza albo eksperyment w drzewie `orx`. Każdy węzeł ma wewnętrzne **`id`** oraz **slug**.
- **`id`** — wewnętrzny identyfikator węzła. Biorą go komendy `orx` na węźle.
- **Slug** — czytelna nazwa węzła (np. `lora-rank-vs-shots`). Powstaje z `--title` przy tworzeniu. To też nazwa brancha `orx/<slug>` i kanału `ai-crew-sync`.
- **`run_id`** — identyfikator jednego uruchomienia joba przy węźle eksperymentu.

## Którego używać

1. **Projekt** → `project_id` (`orx create-experiment`, `orx project view`, `orx runs` z projektem).
2. **Komenda na węźle** (`orx exp status` / `desc` / `run` / `cancel` / `wake` / `wait`, `--parent`) → wewnętrzne **`id`**.
3. **Branch, kanał, nazwa robocza** → **slug**.
4. **Logi / status jednego joba** → **`run_id`** (`orx logs <run_id>`).

Po utworzeniu węzła zapisz wypisane `id`. Gdy go nie masz: `orx project view <project_id>` (lista: `id`, tytuł, branch).

## `project_id`

Cała praca badawcza siedzi w jednym projekcie `orx`. `project_id` jest stały.

`project_id`: `<nieustawiony — uzupełnia użytkownik po utworzeniu projektu; komenda: orx projects>`

Źródło wartości, w tej kolejności:

1. ten plik (gdy użytkownik wpisze tu wartość — to źródło prawdy);
2. brief / zlecenie;
3. `orx projects`;
4. pytanie na kanale `project` albo do nadawcy.

Projekt `orx` zakłada użytkownik.

## `id` i slug węzła

- Komendy `orx` na węźle biorą **`id`**, nie slug.
- Slug generuje `orx` z `--title`. Nie ma osobnej flagi na slug.
- Slug zostaje przy węźle na stałe.
- Nowy wariant pytania albo inna logika porównania = **nowy węzeł** (nowy slug), zwykle dziecko istniejącego (`--parent <id>`).

Tworzenie węzłów: `hypotheses.md`, `experiments.md`. Branch i worktree: `worktrees.md`. Kanał = slug: `common/communication.md`.

## Runy

Pojedyncze uruchomienie = run `orx`:

- lista: `orx runs <project_id> [--experiment <id>]`
- logi: `orx logs <run_id>`
