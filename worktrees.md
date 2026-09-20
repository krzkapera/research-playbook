# Worktrees i środowisko

## orx już to robi automatycznie

Każda sesja `orx up` dostaje własny, prywatny worktree automatycznie — `orx` tworzy go przy pierwszym kroku sesji, w stałej, samonaprawiającej się lokalizacji, wypisany na baseline w stanie `detached`. **Nie uruchamiaj `git worktree add`** wewnątrz takiej sesji — worktree już istnieje, zanim napiszesz pierwszą wiadomość. Jedyne, co robisz sam, to `git checkout orx/<slug>`, żeby przejść na branch eksperymentu, nad którym pracujesz.

To dotyczy też sesji spawnowanych przez `orx agent spawn` — delegowany pomocnik dostaje swój własny worktree tym samym mechanizmem, nie branch bieżącej sesji.

## Pułapka: natywny subagent modelu to nie nowa sesja orx

Ma to znaczenie przy wielu równoległych programistach. Worktree jest przypisany do procesu uruchomionego przez `orx up` (jeden `ORX_CHAT_SESSION_ID` na proces), nie do rozmowy w ogólności — to nieudokumentowane nigdzie wprost, ustalone przez śledzenie kodu.

- **`orx agent spawn`** tworzy nowy proces najwyższego poziomu — nowy `session_id`, więc nowy, osobny worktree. Bezpieczne dla równoległej pracy nad różnym kodem.
- **Natywny subagent narzędzia** (Task tool w Claude Code, odpowiednik w Cursor/Antigravity) działa wewnątrz tego samego procesu — nie dostaje nowego `session_id` ani worktree. Dzieli worktree i branch ze swoim rodzicem.

Jeżeli programistów ma być wielu, każdy edytujący inny kod naraz, **każdy musi być osobną sesją `orx up` (albo `orx agent spawn`)**, nie natywnym subagentem w ramach jednej sesji. Natywny subagent nadaje się do zadań, które nie dotykają tego samego worktree co rodzic równolegle z nim (np. krótkie zapytanie, analiza tekstu) — nie do pisania kodu obok rodzica.

## Jeden worktree na sesję, nie jeden na branch

Worktree jest przypisany do sesji (rozmowy), nie do konkretnego węzła. Jeśli w ramach jednej sesji przechodzisz do pracy nad innym eksperymentem, robisz kolejny `git checkout orx/<inny-slug>` w tym samym worktree — nie tworzysz nowego.

## Konflikt dwóch sesji na tym samym branchu

`orx` nie pilnuje tego przy tworzeniu worktree (każda sesja dostaje swój, zawsze `detached` na starcie) — konflikt pojawia się dopiero, gdy dwie sesje spróbują `checkout` tego samego brancha `orx/<slug>`; wtedy Git odmawia drugiej z nich. Zanim zaczniesz pracować nad branchem, sprawdź `git branch -a` — czy ktoś już go ma wybrany. To umowa społeczna, nie mechanizm narzędzia.

## Kiedy ręczny `git worktree add` jest właściwy

Tylko poza sesją `orx up` — np. proces na hoście, który sprawdza albo diffuje kod eksperymentu bez przechodzenia przez `orx`. Jeśli to robisz, trzymaj się nazewnictwa brancha `orx/<slug>` (nie własnego schematu), żeby `orx project view <project_id>`/`orx runs <project_id>` nadal widziały ten worktree jako część drzewa.

## Przed pracą

- przeczytaj dokumenty wskazane przez `access-matrix.md` i opis węzła hipotezy;
- `git checkout orx/<slug>`, sprawdź commit bazowy i czystość worktree;
- wykonaj najtańszy smoke test przed większą zmianą;
- nie modyfikuj brancha, który wg `git branch -a` jest już zajęty przez inną sesję.

Przy pracy wyłącznie nad opisem węzła (bez zmiany kodu) checkout brancha nie jest potrzebny — edytujesz `description` przez `orx exp desc`.

## Po pracy

Agent przekazuje:

- branch i commit;
- zmienione pliki;
- wykonane komendy i testy;
- artefakty oraz logi;
- problemy i nieweryfikowane założenia.

Scalenie brancha jest decyzją zespołu, nie automatycznym skutkiem zakończenia agenta.

## HPC

Kod i skrypty HPC powstają w Twoim sesyjnym worktree, ale job zapisuje logi i wyniki w jawnej lokalizacji opisanej w eksperymencie (patrz `file-lifecycle.md`). Job musi być wznawialny. Monitorowanie joba idzie przez `orx exp wait`/`orx exp wake`, a nie przez własną pętlę bash — chyba że chodzi o coś, czego `orx` nie pokrywa, jak przełączanie klastra przy zapchanej kolejce (patrz `programmer.operator.md`).
