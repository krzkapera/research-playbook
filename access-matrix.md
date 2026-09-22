# Macierz dostępu do dokumentacji

Agent czyta wyłącznie:

1. pliki z `common/`;
2. przekazany mu plik persony z `roles/`, oraz pliki domenowe, do których on odsyła — to on sam mówi, czego jeszcze potrzebuje, nie osobna tabela (patrz `README.md`);
3. bieżący węzeł hipotezy/eksperymentu w `orx` albo artefakt jawnie wskazany w zleceniu;
4. `research-brief.md` — brief badawczy, dozwolony każdemu, gdy jego plik persony każe go przeczytać albo gdy potrzebuje sprawdzić zakres/ograniczenia badania.

Czytaj wyłącznie pliki wskazane w tej macierzy oraz te, do których odsyła Twoja persona. Gdy brakuje informacji, zapytaj albo poproś o wskazanie pliku.

## Wspólne dla wszystkich ról

Każdy agent czyta:

- `common/rules.md`
- `common/communication.md`
- `common/identifiers.md`
- `agent-start.md`
- przekazany plik roli

Te pliki zawierają tylko zasady potrzebne wszystkim rolom.

Dodatkowo wolno czytać `README.md` (mapa person) oraz `model-assignment.md` (gdy spawnujesz albo dobierasz harness/model).

## Łączenie zakresów

Jeżeli agent ma kilka przekazanych person naraz (np. okrojony skład z `model-assignment.md`, gdzie jeden agent jest jednocześnie professorem i laborantem), sumuje lektury ze wszystkich przekazanych plików person i czyta wyłącznie tę sumę. Jak kontynuować w jednej sesji zamiast spawnu „jako siebie” — patrz `model-assignment.md`, „Połączone persony w jednej sesji”. Przykład: `professor.md` + `laborant.md` razem czytają `common/*`, oba pliki person i domeny, do których odsyłają (`research-brief.md`, `hypotheses.md`, `experiments.md`, `professor-laborant.decision-maker.md`). `worktrees.md` wchodzi dopiero gdy persona implementuje kod.

## Źródło prawdy

Cała dokumentacja `project/*.md` oraz katalogi `common/` i `roles/` są read-only dla agentów. Zmienia je wyłącznie użytkownik. Stan badań (hipotezy, eksperymenty) nie jest częścią tej dokumentacji — żyje jako węzły `orx` i kanały/zadania `ai-crew-sync`, edytowany przez agenta aktualnie odpowiedzialnego za dany węzeł (patrz `common/communication.md`, "Kto edytuje węzeł"). Jedyny wyjątek od read-only: `literature/index.md`, dopisywany przez `librarian` pod lockiem `ai-crew-sync` (patrz `roles/librarian.md`).
