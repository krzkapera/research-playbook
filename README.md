# Projekt

To jest zestaw instrukcji operacyjnych dla wieloagentowego projektu badawczego.

## Dostęp do dokumentacji

Nie ma jednej listy plików dla wszystkich agentów. Agent czyta `access-matrix.md`, pliki `common/`, przekazany plik roli i tylko zakres domenowy wskazany dla tej roli. Nie czyta plików innych ról ani dokumentów spoza macierzy.

## Kanały

- `project` — wspólne decyzje, ogłoszenia i przekrojowa komunikacja.
- kanał nazwany slugiem węzła `orx` — osobny dla każdej aktywnej hipotezy i każdego aktywnego eksperymentu.
- P2P — pytania, delegowanie i odpowiedzi między konkretnymi agentami.

Kanał nie zastępuje trwałego zapisu. Hipotezy i eksperymenty żyją jako węzły drzewa `orx` (`description`, patrz `hypotheses.md`/`experiments.md`), nie jako treść kanału.

Cała dokumentacja `project/*.md` oraz katalogi `common/` i `roles/` są read-only dla agentów — opisują procedury, nie konkretne obiekty badawcze. `file-lifecycle.md` i `access-matrix.md` definiują odpowiedzialność oraz zakres odczytu. Węzeł `orx` edytuje agent aktualnie za niego odpowiedzialny — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz.

## Źródło bieżącej roli

Przy uruchomieniu przekazujesz agentowi **jeden plik persony**: `roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`. Agent nie rozpoznaje roli samodzielnie; ustala ją wyłącznie z tego, co przekazałeś. Jeden agent może mieć wiele person naraz (patrz `model-assignment.md` — okrojony skład).

Plik persony zawiera całą treść, której persona potrzebuje zawsze — z jednym wyjątkiem: domenę `decision-maker`, bo tę samą treść współdzieli professor (poziom hipotezy) i laborant (poziom eksperymentu), więc żeby jej nie duplikować, została osobnym plikiem, do którego oba pliki person odsyłają. Podobnie `programmer.operator.md` został osobnym plikiem, bo jest doklejany do programisty tylko warunkowo (gdy zadanie obejmuje HPC), nie zawsze. Poza tymi dwoma wyjątkami nie ma dalszego rozbicia na pliki domenowe — nie ma po co, skoro reszta domen i tak należy zawsze do dokładnie jednej persony.

Pliki w `roles/` są konfigurowane wyłącznie przez użytkownika. Agenci nie edytują ich.

## Plik persony a jego wyjątki

| Persona | Plik persony | Dodatkowo odsyła do |
|---|---|---|
| professor | `professor.md` | `professor-laborant.decision-maker.md` (poziom hipotezy) |
| laborant | `laborant.md` | `professor-laborant.decision-maker.md` (poziom eksperymentu) |
| programmer | `programmer.md` | `programmer.operator.md`, tylko gdy zadanie obejmuje HPC |
| critic | `critic.md` | — |
| librarian | `librarian.md` | — |

Nie ma osobnej tożsamości „hpc-assistant" — monitorowanie kolejki i przełączanie klastra to `programmer.operator.md`, doklejany do `programmer.md`, gdy zadanie tego wymaga, albo zlecany samodzielnie innej instancji.
