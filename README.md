# Projekt

To jest zestaw instrukcji operacyjnych dla wieloagentowego projektu badawczego.

## Dostęp do dokumentacji

Każdy agent czyta: `access-matrix.md`, pliki `common/`, przekazany plik roli oraz pliki domenowe, do których ten plik odsyła. To jest pełny zestaw lektur dla agenta.

## Kanały

- `project` — wspólne decyzje, ogłoszenia i przekrojowa komunikacja.
- kanał nazwany slugiem węzła `orx` — osobny dla każdej aktywnej hipotezy i każdego aktywnego eksperymentu.
- P2P — pytania, delegowanie i odpowiedzi między konkretnymi agentami.

Kanał nie zastępuje trwałego zapisu. Hipotezy i eksperymenty żyją jako węzły drzewa `orx` (`description`, patrz `hypotheses.md`/`experiments.md`), nie jako treść kanału.

Cała dokumentacja `project/*.md` oraz katalogi `common/` i `roles/` są read-only dla agentów — opisują procedury, nie konkretne obiekty badawcze. `access-matrix.md` definiuje zakres odczytu; `common/communication.md` definiuje odpowiedzialność za węzeł. Węzeł `orx` edytuje agent aktualnie za niego odpowiedzialny — to rola, nie stała tożsamość instancji, bo laborantów i programistów może być wielu naraz.

## Źródło bieżącej roli

Przy uruchomieniu przekazujesz agentowi **jeden plik persony**: `roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`. Agent nie rozpoznaje roli samodzielnie; ustala ją wyłącznie z tego, co przekazałeś. Jeden agent może mieć wiele person naraz (patrz `model-assignment.md` — okrojony skład).

Plik persony zawiera całą treść, której persona potrzebuje zawsze — z jednym wyjątkiem: domenę `decision-maker`, bo tę samą treść współdzieli professor (poziom hipotezy) i laborant (poziom eksperymentu), więc żeby jej nie duplikować, została osobnym plikiem, do którego oba pliki person odsyłają. Podobnie `programmer.operator.md` został osobnym plikiem, bo jest doklejany do programisty tylko warunkowo (gdy zadanie obejmuje HPC), nie zawsze. Poza tymi dwoma wyjątkami nie ma dalszego rozbicia na pliki domenowe — nie ma po co, skoro reszta domen i tak należy zawsze do dokładnie jednej persony.

Pliki w `roles/` konfiguruje wyłącznie użytkownik. Agenci traktują je jako tylko do odczytu.


## Persony a domeny

Jedynymi tożsamościami agentów są **persony**: `professor`, `laborant`, `programmer`, `critic`, `librarian`. Tak się przedstawiają, tak się je adresuje, tak się je spawnuje.

Pliki `professor-laborant.decision-maker.md` i `programmer.operator.md` to **dodatki domenowe** do person: współdzielona albo warunkowa treść proceduralna doklejana do istniejącej persony. Jedyne persony do wołania to: professor, laborant, programmer, critic, librarian. Słowa w rodzaju researcher, experiment-designer, analyst, implementer, hpc-assistant oznaczają czynności wewnątrz persony.

Mapowanie czynności → persona:

| Czynność | Kto |
|---|---|
| hipoteza, kierunek badania, decyzja na poziomie hipotezy | `professor` (+ dodatek decision-maker) |
| projekt eksperymentu, analiza wyników, decyzja na poziomie eksperymentu | `laborant` (+ dodatek decision-maker) |
| implementacja kodu | `programmer` |
| joby HPC / kolejka / klaster | `programmer` + dodatek `programmer.operator.md` |
| krytyka merytoryczna | `critic` (oraz każdy, gdy critic nieaktywny) |
| literatura | `librarian` |

## Plik persony a jego wyjątki

| Persona | Plik persony | Dodatkowo odsyła do |
|---|---|---|
| professor | `professor.md` | `professor-laborant.decision-maker.md` (poziom hipotezy) |
| laborant | `laborant.md` | `professor-laborant.decision-maker.md` (poziom eksperymentu) |
| programmer | `programmer.md` | `programmer.operator.md`, tylko gdy zadanie obejmuje HPC |
| critic | `critic.md` | — |
| librarian | `librarian.md` | — |

Monitorowanie kolejki i przełączanie klastra opisuje `programmer.operator.md`: doklejasz do `programmer.md`, gdy zadanie tego wymaga, albo zlecasz samodzielnie innej instancji czytającej oba pliki.
