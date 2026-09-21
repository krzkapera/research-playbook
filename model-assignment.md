# Przydział modeli i krotność

To ustalenia operacyjne, nie treść ról — opisują, które subskrypcje/modele stoją za którą rolą, i co robisz Ty jako operator, gdy dana subskrypcja trafi na limit. Agent nie potrafi sam przełączyć modelu, który go napędza — to wymaga ręcznego uruchomienia innego narzędzia przez Ciebie. Jedynym wyjątkiem jest librarian: tam "przełączenie" dotyczy źródła wyszukiwania w obrębie jednej sesji, nie modelu — agent robi to sam, bez Twojej interwencji.

`orx agent spawn --harness <x>` przyjmuje tylko zamknięty zbiór harnessów (`claude-code`, `codex`, `cursor`, `antigravity`, `opencode`) — nie da się nim odpalić sesji na Google AI Studio, bo to nie jest żaden z nich. Domyślny backend librariana idzie więc przez Twoje własne skrypty, nie przez `orx agent spawn`; dopiero zapasowy Gemini (Antigravity) da się faktycznie zaspawnować.

| Rola | Model / harness | Krotność | Gdy subskrypcja trafi na limit |
|---|---|---|---|
| professor | zawsze najmocniejszy dostępny model (obecnie Codex; docelowo możliwe przejście na Opusa) | 1× | czekasz na odnowienie; nigdy nie podstawiasz słabszego modelu |
| laborant | Opus (Claude Code) | N× równolegle, jedna instancja na hipotezę | czekasz na odnowienie |
| critic | Grok (Cursor) | N× równolegle, jedna instancja na wątek (hipoteza/eksperyment) | czekasz na odnowienie; do tego czasu praca idzie dalej bez tej perspektywy |
| programmer | Gemini (Antigravity) | N× równolegle, każdy jako osobna sesja `orx up`/`orx agent spawn` — **nie** natywny subagent narzędzia w jednej sesji, bo dzieliłby worktree (patrz `worktrees.md`) | czekasz na odnowienie |
| librarian | Google AI Studio free tier, dopóki starcza limitu (przez Twoje skrypty); potem Gemini (Antigravity, przez `orx agent spawn --harness antigravity`) | spawn/uruchomienie na pojedyncze zapytanie, kończone po odpowiedzi | agent sam przełącza źródło wyszukiwania w obrębie sesji — patrz `roles/librarian.md` |

## Minimalny setup

Gdy Codex i/lub Cursor są niedostępne, projekt działa w okrojonym składzie: Opus pełni jednocześnie professor i laborant, Gemini pełni programmer. W tym składzie nie ma critica — praca idzie do przodu bez tej perspektywy.
