# Wspólne identyfikatory

Ten plik ustala nazwy projektu, węzłów i runów `orx`, którego identyfikatora używać w komendach oraz miejsca zapisu wyników.

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

Cała praca badawcza siedzi w jednym projekcie `orx`. `project_id` jest stały w danej sesji.

Ustal go sam na starcie sesji, w tej kolejności:

1. brief / zlecenie — gdy podaje `project_id` albo jednoznaczną nazwę lub ścieżkę repo projektu;
2. kontekst sesji `orx` — helper ze `orx agent spawn` dziedziczy projekt rodzica;
3. `orx projects` — wybierz wpis zgodny z katalogiem roboczym lub nazwą repo projektu badawczego; przy dokładnie jednym pasującym kandydacie weź go;
4. gdy nadal niejednoznaczne — krótko dopytaj nadawcę briefu (`communication.md` § Roundtrip).

Zapamiętaj wybrane `project_id` w sesji i wstawiaj je do komend `orx` oraz do briefów spawnu (placeholdery `<project_id>`).

Projekt `orx` (repo + import w UI) zakłada użytkownik; agent tylko odczytuje `project_id`.

## `id` i slug węzła

- Komendy `orx` na węźle biorą **`id`**, nie slug.
- Slug generuje `orx` z `--title`. Nie ma osobnej flagi na slug.
- Slug zostaje przy węźle na stałe.
- Nowy wariant pytania albo inna logika porównania = **nowy węzeł** (nowy slug), zwykle dziecko istniejącego (`--parent <id>`).

Tworzenie węzłów: `hypotheses.md`, `experiments.md`. Branch i worktree: `roles/programmer.md` (sekcja Worktree). Kanał = slug: `communication.md` § Kanały.

## Runy

Pojedyncze uruchomienie = run `orx`:

- lista: `orx runs <project_id> [--experiment <id>]`
- logi: `orx logs <run_id>`

## Miejsca zapisu

| Co | Gdzie |
|---|---|
| raporty, notatki, analizy, wykresy, obrazy, CSV, PDF i inne trwałe wyniki (katalog `research/`, także gdy brief użytkownika każe zapisywać wyniki do `research/`) | katalog artefaktów `orx`: `<Artifacts directory>/research/<slug>/…`, gdzie `<slug>` to węzeł, którego dotyczy plik; absolutną ścieżkę katalogu artefaktów `orx` podaje w prompcie każdej sesji jako „Artifacts directory”; w wiadomościach i `description` link `artifacts/research/<slug>/…`. `research/` nie istnieje w repozytorium: nie zakładasz go w worktree i nie commitujesz tych plików do gita |
| kopie cudzego kodu i źródeł do wglądu (paper, repo referencyjne) | `<Artifacts directory>/research/<slug>/sources/…`, z licencją źródła; kod potrzebny do runu trafia jako commit na branch eksperymentu `orx/<slug>` (programmer) |
| kod, konfiguracja, małe pliki wniosku; `job.sbatch` (pisze operator, `roles/operator.md` § job.sbatch) | commit na branchu `orx/<slug>` w worktree sesji; commitujesz przed oddaniem, niezacommitowane zmiany znikają razem z worktree po końcu sesji |
| wyniki runów (log, `exit_code`, pliki zapisane przez job) | `remoteRoot/runs/<runId>/` na klastrze (`orx logs <run_id>`) |
| brief spawnu | zapisuje rola spawnująca: `<Artifacts directory>/<slug>/briefs/<rola>.md`, gdzie `<slug>` to węzeł z briefu, a `<rola>` to rola spawnowanej sesji; komenda spawnu czyta ten plik przez `--stdin`; w wiadomościach link `artifacts/<slug>/briefs/<rola>.md` |
| literatura: PDF-y, ich wersje tekstowe, spis | `~/literature/<nazwa pliku>.pdf`, `~/literature/txt/<nazwa pliku>.txt`, `~/literature/index.md` na hoście, na którym pracują agenci; zapisuje `librarian` (`roles/librarian.md` § Zapis korpusu), pozostałe role czytają |
| stan, ustalenia i decyzje węzła | `description` węzła; nie kopiujesz `description` do plików (np. `hypothesis.md`) |
