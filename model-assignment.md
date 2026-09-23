# Przydział modeli

Przy `orx agent spawn` ustaw `--harness` i `--model` wyłącznie według tabeli poniżej.

| Rola | Harness / model |
|---|---|
| laborant | Claude Code / Opus |
| critic | Cursor / Grok |
| programmer | Antigravity / Gemini |
| librarian | OpenCode / Gemini |

## Połączone role w jednej sesji

Gdy sesja dostała kilka plików ról (np. `professor.md` + `laborant.md`):

- wykonuj etapy **w tej samej sesji**, w kolejności wynikającej z ról;
- szablon professor→laborant stosuj tylko gdy laborant ma być **osobną** sesją; przy połączonych rolach pomiń ten spawn;
- wolno spawnować role, których nie masz (programmer, librarian, critic).
