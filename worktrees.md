# Worktrees i środowisko

## Automatyczny worktree

Każda sesja `orx up` (także po `orx agent spawn`) dostaje własny, prywatny worktree — `orx` tworzy go na starcie, na baseline w stanie `detached`. Przed pracą nad kodem: `git checkout orx/<slug>`.

## Równoległa praca

Równolegli programiści przy różnym kodzie: `orx agent spawn` (osobna sesja, osobny worktree). Natywny subagent modelu: krótkie zapytania i analiza tekstu.

## Jeden worktree na sesję

Worktree należy do sesji, nie do brancha. Inny eksperyment w tej samej sesji = kolejny `git checkout orx/<inny-slug>` w tym samym worktree.

Konflikt: dwie sesje na tym samym `orx/<slug>` — Git odmówi drugiego checkoutu. Zanim wejdziesz na branch, sprawdź `git branch -a`.

Ręczny `git worktree add` tylko poza `orx up` (np. narzędzie na hoście). Nazewnictwo worktree: `orx/<slug>`.

## Przed / po pracy z kodem

Przed: `git checkout orx/<slug>`, sprawdź bazowy commit i czystość worktree, zrób najtańszy smoke test przed większą zmianą. Nie ruszaj brancha, który inna sesja już ma wybrany.

Gdy edytujesz tylko `description` (`orx exp desc`) — checkout nie jest potrzebny.

Po: oddaj branch i commit, zmienione pliki, komendy i testy, ścieżki artefaktów/logów, problemy i niezweryfikowane założenia.
