# Przydział modeli

Przy `orx agent spawn` uruchom **dokładnie komendę z tabeli** dla danej roli. Brief zapisujesz do pliku według `identifiers.md` § Miejsca zapisu; w komendzie za `<Artifacts directory>` wstawiasz absolutną ścieżkę katalogu artefaktów, a za `<slug>` slug węzła z briefu. Flagi harness/model/permission/reasoning i `--no-wake` są już w komendzie.

| Rola | Komenda |
|---|---|
| laborant | `orx agent spawn --no-wake --harness claude-code --model 'claude-opus-5-5[1m]' --permission-mode bypassPermissions --stdin < <Artifacts directory>/<slug>/briefs/laborant.md` |
| critic | `orx agent spawn --no-wake --harness cursor --model grok-4.7-medium --permission-mode full-access --stdin < <Artifacts directory>/<slug>/briefs/critic.md` |
| programmer | `orx agent spawn --no-wake --harness antigravity --model gemini-3.8-flash-medium --reasoning-level medium --permission-mode bypass --stdin < <Artifacts directory>/<slug>/briefs/programmer.md` |
| operator | `orx agent spawn --no-wake --harness opencode --model opencode/muse-spark-1.3-contributor-free --reasoning-level medium --permission-mode auto-approve --stdin < <Artifacts directory>/<slug>/briefs/operator.md` |
| librarian | `orx agent spawn --no-wake --harness opencode --model google/gemini-3.8-flash --reasoning-level low --permission-mode auto-approve --stdin < <Artifacts directory>/<slug>/briefs/librarian.md` |

## Połączone role w jednej sesji

Programmer i operator zawsze pracują jako osobne sesje: operatora spawnuje programmer (`roles/programmer.md` § Szablon spawnu → operator).

Gdy sesja dostała kilka plików ról (np. `professor.md` + `laborant.md`):

- etapy prowadź **w tej samej sesji**, w kolejności flow roli „głównej” (pierwsza w briefie / zleceniu) z wchłoniętymi etapami pozostałych;
- na kanale publikuj osobny wpis dla każdej roli, każdy z etykietą tej roli (`communication.md` § Pojęcia);
- szablon professor→laborant stosuj tylko gdy laborant ma być **osobną** sesją; przy połączonych rolach pomiń ten spawn;
- wolno spawnować role, których nie masz (programmer, operator, librarian, critic), jak w `access-matrix.md`.
