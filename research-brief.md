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

- **Źródło prawdy w projekcie:** `literature/` — PDF-y i spis `literature/index.md` w jednym katalogu (bez podkatalogów). Utrzymuje `librarian`. Agenci nie zapisują paperów poza `literature/`.

Przepływ: najpierw papery już zebrane, potem szerokie wyszukiwanie (własne narzędzia + firecrawl jako MCP — search / research index). Czytaj abstrakty; przy dopasowaniu — całość i zapis do korpusu. Pliki tymczasowe i wyniki w katalogu projektu.

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


## Tematy do zresearchowania

Vision: few-shot self-supervised; few-shot anomaly detection; few-shot continual learning; backbones / foundation (ViT, CNN); continual learning; fine-tuning; augmentation; regularyzacje przy małej liczbie przykładów klasy (reszta = inne zdjęcia); warianty LoRA; porównanie trening parametrów vs training-free.
