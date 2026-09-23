# Przydział modeli i krotność

Przy `orx agent spawn` podaj `--harness` i `--model` z tabeli (lub z szablonu w pliku roli). Nazwy modeli bywają zmienne — gdy w szablonie jest placeholder, sprawdź aktualną listę narzędziem harnessu (np. `agy models`, `opencode models`).

| Rola | Harness / model | Krotność |
|---|---|---|
| professor | najmocniejszy dostępny (obecnie Codex) | 1× |
| laborant | Claude Code / Opus | N× równolegle, jedna sesja na hipotezę |
| critic | Cursor / Grok | N× równolegle, jedna sesja na wątek (hipoteza albo eksperyment) |
| programmer | Antigravity / Gemini | N× równolegle, każda jako osobna sesja `orx` (nie natywny subagent — `worktrees.md`) |
| librarian | najpierw `opencode` + `google/<model-id>` (`GEMINI_API_KEY`); potem Antigravity / Gemini | spawn na jedno zapytanie; przełączanie źródła w sesji: `roles/librarian.md` |

## Okrojony skład

Gdy Codex i/lub Cursor są niedostępne: Opus = professor + laborant w jednej sesji, Gemini = programmer. Bez critica — praca idzie dalej bez tej perspektywy.

## Połączone role w jednej sesji

Gdy sesja dostała kilka plików ról (np. `professor.md` + `laborant.md`):

- wykonuj etapy **w tej samej sesji**, w kolejności wynikającej z ról;
- szablon professor→laborant stosuj tylko gdy laborant ma być **osobną** sesją; przy połączonych rolach pomiń ten spawn;
- wolno spawnować role, których nie masz (programmer, librarian, critic).
