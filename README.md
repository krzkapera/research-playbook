# Projekt

To jest zestaw instrukcji operacyjnych dla wieloagentowego projektu badawczego.

## Dostęp do dokumentacji

Nie ma jednej listy plików dla wszystkich agentów. Agent czyta `access-matrix.md`, pliki `common/`, przekazany plik roli i tylko zakres domenowy wskazany dla tej roli. Nie czyta plików innych ról ani dokumentów spoza macierzy.

## Kanały

- `project` — wspólne decyzje, ogłoszenia i przekrojowa komunikacja.
- kanał nazwany slugiem węzła `orx` — osobny dla każdej aktywnej hipotezy i każdego aktywnego eksperymentu.
- P2P — pytania, delegowanie i odpowiedzi między konkretnymi agentami.

Kanał nie zastępuje trwałego zapisu. Hipotezy i eksperymenty żyją jako węzły drzewa `orx` (`description`, patrz `hypotheses.md`/`experiments.md`), nie jako treść kanału.

Cała dokumentacja `project/*.md` oraz katalogi `common/`, `roles/` i `templates/` są read-only dla agentów — opisują procedury, nie konkretne obiekty badawcze. `file-lifecycle.md` i `access-matrix.md` definiują odpowiedzialność oraz zakres odczytu. Węzeł `orx` edytuje agent aktualnie za niego odpowiedzialny — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz.

## Źródło bieżącej roli

Przy uruchomieniu przekazujesz agentowi **jeden plik persony**: `roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`. Agent nie rozpoznaje roli samodzielnie; ustala ją wyłącznie z tego, co przekazałeś. Jeden agent może mieć wiele person naraz (patrz `model-assignment.md` — okrojony skład).

Plik persony jest jednak tylko punktem wejścia. Dla professora, laboranta i programisty odsyła dalej do właściwych plików domenowych w `roles/` (bo te domeny bywają współdzielone albo opcjonalne — patrz "Domeny" niżej); critic i librarian są jednodomenowe, więc ich plik persony i plik domenowy to to samo. Agent czyta plik persony, a potem — sam, ze swoim dostępem do plików — wszystko, do czego on odsyła.

Pliki w `roles/` są konfigurowane wyłącznie przez użytkownika. Agenci nie edytują ich.

## Domeny pod personami

Domeny w `roles/` są celowo drobnoziarniste, żeby dało się je swobodnie komponować — stąd nazwy plików domenowych w formacie `<persona(-y)>.<domena>.md`: sama nazwa pliku mówi, do której persony należy i jaka jest jej domena.

| Persona | Plik persony | Domeny, do których odsyła |
|---|---|---|
| professor | `professor.md` | `professor-laborant.decision-maker.md` (poziom hipotezy) + `professor.researcher.md` |
| laborant | `laborant.md` | `laborant.experiment-designer.md` + `professor-laborant.decision-maker.md` (poziom eksperymentu) + `laborant.analyst.md` |
| programmer | `programmer.md` | `programmer.implementer.md`, opcjonalnie + `programmer.operator.md` gdy zadanie obejmuje HPC |
| critic | `critic.md` | (jednodomenowa) |
| librarian | `librarian.md` | (jednodomenowa) |

Nie ma osobnej tożsamości „hpc-assistant" — monitorowanie kolejki i przełączanie klastra to domena `programmer.operator.md`, doklejana do `programmer.implementer.md`, gdy zadanie tego wymaga, albo zlecana samodzielnie innej instancji.
