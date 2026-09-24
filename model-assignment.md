# Przydział modeli

Przy `orx agent spawn` ustaw `--harness` i `--model` wyłącznie według tabeli poniżej.

| Rola | Harness / model |
|---|---|
| laborant | Claude Code / Opus |
| critic | Cursor / Grok |
| programmer | Antigravity / Gemini |
| operator | Antigravity / Gemini |
| librarian | OpenCode / Gemini |

## Połączone role w jednej sesji

Gdy sesja dostała kilka plików ról (np. `professor.md` + `laborant.md`):

- etapy prowadź **w tej samej sesji**, w kolejności flow roli „głównej” (pierwsza w briefie / zleceniu) z wchłoniętymi etapami pozostałych;
- na kanale publikuj **osobne** wpisy z jawną etykietą roli, np. `[professor]` / `[laborant]`;
- szablon professor→laborant stosuj tylko gdy laborant ma być **osobną** sesją; przy połączonych rolach pomiń ten spawn;
- wolno spawnować role, których nie masz (programmer, operator, librarian, critic) — jak w `access-matrix.md`.
