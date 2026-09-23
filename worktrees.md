# Worktrees i środowisko

## Automatyczny worktree

Każda sesja `orx up` (także po `orx agent spawn`) dostaje własny, prywatny worktree — `orx` tworzy go na starcie, na baseline w stanie `detached`. Przed pracą nad kodem: `git checkout orx/<slug>`.

## Spawn vs natywny subagent

- `orx agent spawn` = nowy proces, nowy `session_id`, **osobny** worktree. Tak uruchamiaj równoległych programistów przy różnym kodzie.
- Natywny subagent modelu (Task w Claude Code, odpowiedniki w Cursor/Antigravity) zostaje w procesie rodzica — **ten sam** worktree i branch. Nie używaj go do równoległej edycji kodu obok rodzica; nadaje się do krótkich zapytań / analizy tekstu.

## Jeden worktree na sesję

Worktree należy do sesji, nie do brancha. Inny eksperyment w tej samej sesji = kolejny `git checkout orx/<inny-slug>` w tym samym worktree.

Konflikt: dwie sesje na tym samym `orx/<slug>` — Git odmówi drugiego checkoutu. Zanim wejdziesz na branch, sprawdź `git branch -a`.

Ręczny `git worktree add` tylko poza `orx up` (np. narzędzie na hoście). Trzymaj nazewnictwo `orx/<slug>`, żeby `orx` nadal widział worktree w drzewie.

## Przed / po pracy z kodem

Przed: `git checkout orx/<slug>`, sprawdź bazowy commit i czystość worktree, zrób najtańszy smoke test przed większą zmianą. Nie ruszaj brancha, który inna sesja już ma wybrany.

Gdy edytujesz tylko `description` (`orx exp desc`) — checkout nie jest potrzebny.

Po: oddaj branch i commit, zmienione pliki, komendy i testy, ścieżki artefaktów/logów, problemy i niezweryfikowane założenia.
