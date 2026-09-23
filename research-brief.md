# Brief badawczy

## Cel i zakres

Opracować metodę **continual visual unsupervised anomaly detection** opartą o trening few-shot (może korzystać z mocnego pretrained backbone’u). Cel: wysoka skuteczność na danych specyficznych (detale), przy **minimalnej** liczbie przykładów do uczenia.

- Trening **parametrów** modelu (preferowane). LoRA / adaptery / inne miejsca wstawienia — do ustalenia; nie musi być LoRA.
- Uczenie może być gradientowe, ale nie musi.
- Budżet shotów: dążyć jak najniżej; akceptowalne 10–20, w ostateczności ~50 (ew. do ~100 przy uzasadnieniu). Benchmark Continual-Mega daje 10 — wolno podnieść w tym limicie.
- Opcjonalnie: bez próbek anomalnych w treningu — pożądane, ale nie twarde wymaganie.
- Kierunek: metody z treningiem parametrów. Zero-shot / training-free / promptowe wolno **czerpać pomysły**, ale same w sobie nie są celem.
- Teza do weryfikacji: uczenie parametrów pozwala uzyskać lepsze wyniki niż metody beztreningowe (zweryfikuj najnowszymi paperami).

Na start: nacisk na **teorię / design metody**; implementacja będzie, ale szczegóły implementacyjne odłóż aż design będzie jasny.

## Literatura

- **Źródło prawdy w projekcie:** `literature/` — PDF-y w `literature/artykuly/` i `literature/fsad/`, spis w `literature/index.md`. Utrzymuje `librarian`. Agenci nie zapisują paperów poza `literature/`.
- **Zbiór startowy użytkownika (tylko odczyt):** `~/agh/pp/artykuly/txt` (klasyczne one-class continual vision AD) oraz `~/agh/pp/fsad/txt` (few-shot AD). Librarian najpierw sprawdza `literature/`; gdy brief lub luka wskazuje na `~/agh/pp/...`, czyta stamtąd i **przydatne rzeczy indeksuje / przenosi do `literature/`**.

Przepływ: najpierw papery już zebrane, potem szerokie wyszukiwanie (własne narzędzia + firecrawl CLI — tylko research index: `docs.firecrawl.dev/sdks/cli`, `docs.firecrawl.dev/features/search`). Czytaj abstrakty; przy dopasowaniu — całość i zapis do korpusu. Pliki tymczasowe i wyniki w katalogu projektu.

Wskazówka: praca **foundAD** (trening parametrów few-shot AD) — kandydat do adaptacji na Continual-Mega; sprawdź zachowanie przy jednej klasie w tasku.

## Benchmarki i dane

- Kotwice: **Continual-Mega**; także **MVTec** i **VisA** uczone klasa-po-klasie (jak w klasycznych pracach z korpusu).
- Konkretny benchmark nie jest dogma — szukamy możliwości w ramach tych kotwic.
- Continual: osobne parametry (lub adaptery) na task → routing może być prosty (np. cechy foundation modelu). W jednym tasku Continual-Mega bywa wiele klas.

## Flow pracy

1. Pomysł / hipoteza metodyczna
2. Research w paperach (nowe; istniejące = baza)
3. Propozycja eksperymentu
4. Kod + obliczenia
5. Wniosek tylko z eksperymentu, rachunku albo literatury

Praca iteracyjna, małymi krokami. Eksperymenty powtarzalne (wiele runów przy losowości). Etapy niezależne prowadź równolegle.

## HPC

Dostęp: `ssh helios`, `ssh athena`, `ssh ares` (dokumentacja Cyfronet: Helios/GPU, Athena, Ares). Helios = ARM — specjalna konfiguracja; wzoruj się na innych projektach w `~/scratch/`.

- Artefakty jobów: `~/scratch/<katalog-projektu>/` (kod, cache, venv, logi — porządek).
- Helios: najmocniejszy (pełne datasety). Ares: małe few-shot. Athena: środek. Bez GPU, gdy wystarczy CPU (Ares).
- Nie zapychaj kolejki. Sygnał problemu: po ~10 min od submitu `squeue --start` bez START TIME, albo START TIME > 24 h — przenieś pracę na inny klaster, wróć gdy kolejka odżyje.
- Job wznawialny; zasoby/timelimit: minimum do wyniku. Efficiency z hpc-jobs sensowna; więcej CPU OK, gdy skraca wall-clock.
- Przed większą zmianą / pierwszym jobem: lokalny smoke. Przy zmianie jednego sprawdzonego parametru wystarczy poprzedni smoke.
- Few-shot liczące się w kilka minut na lokalnym GPU → lokalnie (venv w katalogu projektu), nie kolejka.
- Czekanie na koniec runów: skrypty bash. Monitorowanie przez `orx` / operatora — szczegóły w `roles/programmer.operator.md`.

## Tematy do zresearchowania

Vision: few-shot self-supervised; few-shot anomaly detection; few-shot continual learning; backbones / foundation (ViT, CNN); continual learning; fine-tuning; augmentation; regularyzacje przy małej liczbie przykładów klasy (reszta = inne zdjęcia); warianty LoRA; porównanie trening parametrów vs training-free.

## Zasady kodu

- Czysty, zwięzły; pakiety / krótkie pliki / małe funkcje o jednej odpowiedzialności.
- Kod naukowy/algorytmiczny — bez nadmiaru testów, wzorców i ceremonii „enterprise”.
- Bez komentarzy i docstringów, chyba że użytkownik poprosi o oznaczenie uwagi.
- Warstwy abstrakcji osobno; w danym miejscu tylko to, czego czytelnik się tam spodziewa.
- Typy ustalone raz i trzymane; bez zbędnych konwersji i try/except „na wszelki wypadek”.
