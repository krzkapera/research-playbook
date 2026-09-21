# Brief badawczy

Chciałbym opracować metodę continual visual unsupervised anomaly detection, która będzie się opierać na treningu few-shot. Może być oparta na mocnym pretrained backbonie. Na początku myślałem, żeby trenować tylko adaptery LoRA (różne odmiany, modyfikacje, do ustalenia w które miejsca modelu dodawane). Ale niekoniecznie to musi być LoRA. Chciałbym jednak trenować parametry modelu, żeby mieć wysoką skuteczność na specyficznych danych, wyszukiwanie anomalii wymaga dużej skuteczności, model musi zwracać uwagę na szczegóły specyficzne dla danych. Uczenie może być gradientowe, ale nie musi. Jednocześnie chcę minimalizować potrzebną liczbę przykładów do uczenia. Nie musi to być 1/2/4 przykładów, może być 10, 20, a nawet 50 by było akceptowalne w ostateczności, po prostu chcę zminimalizować liczbę przykładów jak się da, zbadać ten temat jak nisko można zejść. Moje dotychczasowe prace są w `~/agh/pp/`. `~/agh/pp/artykuly/txt` to klasyczne prace w temacie one class continual vision anomaly detection. `~/agh/pp/fsad/txt` to najnowszy research w temacie few-shot anomaly detection. Skoro dla każdego taska continual learning trenujemy osobne parametry, to możemy stosować w zasadzie do uczenia few-shot wszystkie pasujące techniki z prac nie-continual. Routing wtedy jest już łatwym problemem, np. na podstawie dobrych cech foundation modelu. Chciałbym, żeby praca była przetestowana na benchmarku Continual-Mega, opcjonalnie najlepiej żeby również nie wymagała próbek anomalnych do treningu, ale to może nie być realne wymaganie. Ten benchmark daje tylko 10 próbek, ale możemy zwiększyć tę liczbę w zależności od potrzeby przy sensownym ograniczeniu max 50, może ew 100. Ten benchmark daje w jednym tasku continual learning wiele klas. Ja bym również chciał, żeby moja metoda działała na klasycznych mvtec i visa uczonych klasa po klasie, tak jak w klasycznych pracach w `artykuly/txt`. Ciekawym znaleziskiem jest praca foundAD, która trenuje parametry na few-shot w problemie anomaly detection, wydaje mi się że łatwo by ją można przerobić na użycie na continual-mega, ale nie jestem pewny czy ta metoda zadziała, gdy w tasku będzie tylko jedna klasa. Konkretny benchmark więc nie jest istotny, do niczego się nie ograniczamy, tylko wyszukujemy jakie są możliwości, z bazowym celem adaptacji do continual-mega oraz klasycznych mvtec i visa uczonych klasa po klasie.

Szczegóły implementacyjne mnie na razie nie interesują, tzn. nie opisuj mi ich, ale implementować też będziemy, tylko samo teoretyczne podejście do takiej metody. Nie interesuje mnie podejście zero-shot, training-free, bezparametrowe, ani promptowe. Choć pomysły z nich można czerpać oczywiście.

Poproszę cię o zrobienie głębokiego researchu w tym temacie. Wyszukiwać możesz swoimi narzędziami oraz dostępnie jest narzędzie firecrawl przez cli (`docs.firecrawl.dev/sdks/cli`, `docs.firecrawl.dev/features/search`), gdzie szukaj tylko w research index. Wyszukuj do woli i szeroko. Czytaj abstrakty, a gdy okazuje się że praca pasuje i ma potencjał, to czytaj całość i zapisuj. Przeanalizuj też najpierw te artykuły, które ja już znalazłem, a następnie spróbuj wyszukać nowe. Wszystkie potrzebne pliki tymczasowe oraz wyniki swojej pracy zapisuj w katalogu projektu. Pozostałe pliki w projekcie masz widoczne po to, żebyś wiedział co ja już znalazłem.

Poza researchem w paperach chodzi też o to, żeby przeprowadzać eksperymenty i weryfikować swoje tezy. Czyli flow pracy powinien być taki, że przychodzi nam do głowy jakiś pomysł, robimy research w paperach (nowych, istniejące służą tylko jako baza), proponujemy eksperyment, piszemy kod, weryfikujemy w obliczeniach.



## Korpus literatury — gdzie czytać i gdzie zapisywać

- **Korpus roboczy zespołu (źródło prawdy w projekcie):** `literature/` — PDF-y w `literature/artykuly/` i `literature/fsad/`, spis w `literature/index.md`. Utrzymuje go persona `librarian`. Agenci nie zapisują paperów poza `literature/`.
- **Mój wcześniejszy zbiór osobisty (punkt startowy, tylko do odczytu):** `~/agh/pp/artykuly/txt` (klasyczne one-class continual vision AD) oraz `~/agh/pp/fsad/txt` (few-shot AD). To nie jest katalog roboczy agentów. Librarian najpierw sprawdza `literature/`; gdy brief lub lukę w indeksie wskazuje na `~/agh/pp/...`, czyta stamtąd i **przydatne rzeczy indeksuje / przenosi do `literature/`**, zamiast trwale polegać na ścieżkach domowych.

## Dostęp do HPC

Te polecenia dają dostęp do HPC:

- `ssh helios` — `docs.hpc.cyfronet.pl/supercomputers/helios/`, `docs.hpc.cyfronet.pl/supercomputers/helios/GPU/`
- `ssh athena` — `docs.hpc.cyfronet.pl/supercomputers/athena/`
- `ssh ares` — `docs.hpc.cyfronet.pl/supercomputers/ares/`

Przeczytaj dokumentację przed użyciem. Helios to ARM, wymaga specjalnej konfiguracji, wzoruj się na innych projektach z `~/scratch/`. Utwórz nowy katalog projektu w `~/scratch/<katalog projektu>` i zapisuj tam WSZYSTKIE swoje pliki, w tym cache itp. Inne projekty już tak robią, zobacz jak to jest zrobione. Venv też raczej utwórz nowy w katalogu projektu, ew. możesz wykorzystać jakiś istniejący venv, jeżeli zawiera wszystkie potrzebne zależności. Pliki `.out` odpowiednio nazywaj i kataloguj, ma być porządek. Między komputerami HPC możesz przenosić pliki, datasety jest łatwiej ściągać bezpośrednio, bo wtedy szybciej idzie.

Najmocniejszy jest helios, do few-shot nie jest nam potrzebny, ale przyda się dla eksperymentów z pełnym datasetem. Do małych few-shot wystarczy ares. Athena jest po środku. Z resztą sam przejrzyj jaki mają sprzęt. A może dla małej liczby przykładów nie będzie potrzeby GPU, to wtedy korzystamy z Aresa.

Uważamy na to, żeby nie zapchać kolejki. Jeżeli przez jakiś czas będziemy zgłaszać dużo jobów, to po jakimś czasie przestaną one wychodzić z kolejki i będą czekać w nieskończoność. Wykryć tą sytuację możemy, gdy po 10min od zgłoszenia `squeue --start` START TIME nie pokazuje czasu, kiedy uruchomi się job. Wtedy zmieniamy superkomputer i tam kontynuujemy prace, aż kolejka na poprzednim się odblokuje, czyli zakolejkowany job dostanie START TIME. Superkomputer zmieniamy też w sytuacji, gdy START TIME pokaże czas uruchomienia joba bardziej odległy niż 24h.

Uruchamiamy eksperymenty jak najbardziej równolegle, nie czekamy z kolejnymi etapami prac, jeżeli nie są wymagane do tych eksperymentów poprzednie wyniki.

Zasobów bierzemy tak, żeby wystarczyło, ale jak najmniej, bo wtedy job szybciej wychodzi z kolejki. Tak samo z timelimit. Nie oszczędzamy, ale też nie bierzemy na zapas. Trening i wszystkie joby za każdym razem pisz tak, żeby dało się wznowić po przerwaniu. Staramy się utrzymać wysoką efficiency pokazywaną przez hpc-jobs, żeby nie marnować grantu, ale też nie dusimy GPU, jeżeli przy większej liczbie CPU job się szybciej policzy, mimo małego efficiency, to wtedy bierzemy więcej CPU, przede wszystkim liczy się szybkość otrzymania wyników. Czyli zgłaszamy joby często, ale odpowiednie, nie zastępujemy myślenia przez masowe eksperymenty, ale też wykorzystujemy to co daje nam HPC. W kolejce mogą być inne moje joby z innych projektów.

Przed każdą większą zmianą, zwłaszcza na początku prac, przed zgłoszeniem joba na hpc, zgłoś lub wykonaj lokalnie smoke test. Przepatrz na hpc pliki .sh i .sbatch z innych projektów. Oczywiście przed zmianą jednego testowanego parametru nie trzeba robić smoke, jeżeli kod wcześniej działał.

Rzeczy na danych few-shot, które się szybko policzą, ani to nie jest batch job eksperyment w wielu wariantach, to wtedy liczymy lokalnie, jeżeli przewidujesz, że się policzy w kilka minut na lokalnym gpu, to wtedy nie ma sensu czekać w kolejce.

Do lokalnych eksperymentów venv utwórz w katalogu projektu. Do czekania na koniec eksperymentów zdalnych i lokalnych używaj skryptów bash.

## Sposób pracy

Research rozrasta się drzewiaście na różne tematy, które potem się zbiegają w spójne wnioski i konkretne pomysły. Nie wymyślamy wielu pomysłów od razu, nie projektujemy niczego od zera do samego końca. Pracujemy powoli, iteracyjnie, małymi krokami, stawiamy hipotezy, weryfikujemy, ale niczego nie przesądzamy, z wnioskami uważamy, zwracamy uwagę na szczegóły. Nie zgadujemy, nie domyślamy się, nie zakładamy z góry. Wszystko ma być uzasadnione naszymi eksperymentami.

Eksperymenty mają być powtarzalne, wykonywane wiele razy, gdy zachodzi taka potrzeba, bo np. dana metoda opiera się na losowości.

## Tematy do zresearchowania

Vision few-shot self-supervised; vision few-shot anomaly detection; vision few-shot continual learning; vision backbones foundation models, vit, cnn; vision continual learning; vision fine tuning; vision augmentation; tego typu regularyzacje na zmniejszanie liczby zdjęć, czyli kilka przykładów naszej klasy, a reszta to inne zdjęcia do regularyzacji, rodzaje metod lora; moja teza — uczenie parametrów pozwala uzyskać lepsze wyniki niż metody beztreningowe, zweryfikuj ją z najnowszymi paperami.

## Eksperymenty proponowane na start

- odtworzyć foundAD wszystkie raportowane wyniki z paperu, mamy otrzymać takie same wyniki lub chociaż w przedziale szumu
- uruchomić foundAD w symulacji continual, czyli trenujemy na całym zbiorze tyle razy ile jest klas, ale ewaluujemy tylko jedną klasę; oczekujemy, że średnio i tak dostaniemy takie same wyniki, jak w treningu łącznym
- jak poprzednio, ale podmieniamy zdjęcia innych klas na inny zbiór, jakiś znany wizyjny, który będzie pasował
- jakieś próby pozbycia się zdjęć innych klas, zmniejszanie liczby zdjęć, inne metody regularyzacji w tym auxiliary anchor
- zmniejszenie głowy, uczenie tylko jej części, później uczenie tylko małego adaptera, natomiast prawdopodobnie architekturę modelu trzeba będzie nieco zmienić, może jakiś pre-trening dodać, choć oczywiście najlepiej by było go uniknąć
- chcemy pokazać, że badania w temacie w ogóle mają sens
- może jakiś teacher-student, np. dwa modele DINO z różnymi głowami, O-LoRA, DoRA, PEFT
- augmentacja i CutPaste wygląda, że dużo pomaga

## Zasady pisania kodu

- kod ma być czysty, wzoruj się na zasadach z książki Wujka Boba Clean Code
- z drugiej strony, to jest kod naukowy, algorytmiczny, kod modelu, wygląda on inaczej niż typowe aplikacje komercyjne, więc nie przesadzaj z testami, zbyt długimi nazwami, wzorcami itp.
- kod ma być zwięzły, uporządkowany, podzielony na pakiety, krótkie pliki, moduły, małe funkcje, które mają po jednej odpowiedzialności
- nigdy nie pisz komentarzy ani docstringów, chyba że poproszę specjalnie o zaznaczenie w komentarzu jakiejś ważnej uwagi
- kod ma mieć małą entropię, nie mieszaj warstw abstrakcji, w danym miejscu ma być zaimplementowana jedynie funkcjonalność, której czytelnik się tam spodziewa, nic nadmiarowego
- typy mają być raz ustalone i potem się ich trzymamy w projekcie, nie stosujemy konwersji na wszelki wypadek, ani nadmiarowych try-catchy
