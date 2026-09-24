# Przydział modeli

Przy `orx agent spawn` użyj dokładnie komendy z tabeli poniżej dla danej roli (dopisz swój `<task>`).

| Rola | Komenda |
|---|---|
| laborant | `orx agent spawn --harness claude-code --model 'claude-opus-5-5[1m]' --permission-mode bypassPermissions "<task>"` |
| critic | `orx agent spawn --harness cursor --model grok-4.7-medium --permission-mode full-access "<task>"` |
| programmer | `orx agent spawn --harness antigravity --model gemini-3.8-flash-medium --reasoning-level medium --permission-mode bypass "<task>"` |
| operator | `orx agent spawn --harness opencode --model opencode/muse-spark-1.3-contributor-free --reasoning-level medium --permission-mode auto-approve "<task>"` |
| librarian | `orx agent spawn --harness opencode --model google/gemini-3.8-flash --reasoning-level low --permission-mode auto-approve "<task>"` |

## Połączone role w jednej sesji

Gdy sesja dostała kilka plików ról (np. `professor.md` + `laborant.md`):

- etapy prowadź **w tej samej sesji**, w kolejności flow roli „głównej” (pierwsza w briefie / zleceniu) z wchłoniętymi etapami pozostałych;
- na kanale publikuj **osobne** wpisy z jawną etykietą roli, np. `[professor]` / `[laborant]`;
- szablon professor→laborant stosuj tylko gdy laborant ma być **osobną** sesją; przy połączonych rolach pomiń ten spawn;
- wolno spawnować role, których nie masz (programmer, operator, librarian, critic) — jak w `access-matrix.md`.
