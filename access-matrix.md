# Macierz dostępu do dokumentacji

Agent czyta wyłącznie:

1. pliki z `common/`;
2. przekazany mu plik persony z `roles/`, oraz pliki domenowe, do których on odsyła (patrz `README.md`);
3. dokumenty wskazane w sekcji jego roli poniżej;
4. bieżący węzeł hipotezy/eksperymentu w `orx` albo artefakt jawnie wskazany w zleceniu;
5. `research-brief.md` — brief badawczy, dozwolony każdemu, gdy jego plik persony każe go przeczytać albo gdy potrzebuje sprawdzić zakres/ograniczenia badania.

Nie czytaj pozostałych plików projektu „na wszelki wypadek”. Nie otwieraj instrukcji innych ról, jeśli nie zostały przekazane. Jeśli brakuje informacji, zapytaj albo poproś o wskazanie pliku.

## Wspólne dla wszystkich ról

Każdy agent czyta:

- `common/rules.md`
- `common/communication.md`
- `common/identifiers.md`
- `agent-start.md`
- przekazany plik roli

Te pliki zawierają tylko zasady potrzebne wszystkim rolom.

## Zakresy domenowe

| Zakres | Dodatkowe pliki |
|---|---|
| hipoteza i decyzja badawcza | `hypotheses.md`, `coordination-flow.md`, bieżący węzeł hipotezy |
| projekt eksperymentu | `experiments.md`, `coordination-flow.md`, bieżące węzły hipotezy i eksperymentu |
| implementacja | `worktrees.md`, bieżące węzły hipotezy i eksperymentu |
| operator/HPC | `worktrees.md`, bieżący węzeł eksperymentu, wskazany job |
| analiza wyników | `experiments.md`, bieżące węzły hipotezy i eksperymentu, wskazane artefakty |
| zmiana dokumentacji systemowej | `file-lifecycle.md`, odpowiedni plik systemowy |

## Łączenie zakresów

Jeżeli agent ma kilka przekazanych person naraz (np. okrojony skład z `model-assignment.md`, gdzie jeden agent jest jednocześnie professorem i laborantem), sumuje ich zakresy, ale nadal nie czyta niczego poza tą sumą. Przykład: `professor.md` + `laborant.md` razem czytają `common/*`, oba pliki person i wszystkie domeny, do których odsyłają, `hypotheses.md`, `experiments.md`, `coordination-flow.md`, bieżące węzły hipotezy i eksperymentu — ale nie `worktrees.md`, bo żadna z tych person nie implementuje kodu.

## Źródło prawdy

Cała dokumentacja `project/*.md` oraz katalogi `common/`, `roles/` i `templates/` są read-only dla agentów. Zmienia je wyłącznie użytkownik. Stan badań (hipotezy, eksperymenty) nie jest częścią tej dokumentacji — żyje jako węzły `orx` i kanały/zadania `ai-crew-sync`, edytowany przez agenta aktualnie odpowiedzialnego za dany węzeł (patrz `file-lifecycle.md`).
