# Projekt

> Dla użytkownika (mapa playbooka). **Agenci nie czytają tego pliku** — ich lektury ustala `access-matrix.md` i przekazana rola.

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

Przy uruchomieniu przekazujesz agentowi **jeden plik roli**: `roles/professor.md`, `roles/laborant.md`, `roles/programmer.md`, `roles/critic.md` albo `roles/librarian.md`. Agent nie rozpoznaje roli samodzielnie; ustala ją wyłącznie z tego, co przekazałeś. Jeden agent może mieć wiele ról naraz (patrz `model-assignment.md` — okrojony skład).

Plik roli zawiera całą treść, której rola potrzebuje zawsze — z jednym wyjątkiem: `programmer.operator.md`, bo jest doklejany do programisty tylko warunkowo (gdy zadanie obejmuje HPC), nie zawsze. Poza tym nie ma rozbicia na pliki domenowe — reszta domen należy zawsze do dokładnie jednej roli.

Pliki w `roles/` konfiguruje wyłącznie użytkownik. Agenci traktują je jako tylko do odczytu.


## Role a domeny

Jedynymi tożsamościami agentów są **role**: `professor`, `laborant`, `programmer`, `critic`, `librarian`. Tak się przedstawiają, tak się je adresuje, tak się je spawnuje.

Plik `programmer.operator.md` to **dodatek domenowy** do roli programmer: warunkowa treść proceduralna doklejana gdy zadanie obejmuje HPC. Jedyne role do wołania to: professor, laborant, programmer, critic, librarian. Słowa w rodzaju researcher, experiment-designer, analyst, implementer, hpc-assistant oznaczają czynności wewnątrz roli.

Mapowanie czynności → rola:

| Czynność | Kto |
|---|---|
| hipoteza, kierunek badania, decyzja na poziomie hipotezy | `professor` |
| projekt eksperymentu, analiza wyników, decyzja na poziomie eksperymentu | `laborant` |
| implementacja kodu | `programmer` |
| joby HPC / kolejka / klaster | `programmer` + dodatek `programmer.operator.md` |
| krytyka merytoryczna | `critic` (oraz każdy, gdy critic nieaktywny) |
| literatura | `librarian` |

## Plik roli a jego wyjątki

| Rola | Plik roli | Dodatkowo odsyła do |
|---|---|---|
| professor | `professor.md` | — |
| laborant | `laborant.md` | — |
| programmer | `programmer.md` | `programmer.operator.md`, tylko gdy zadanie obejmuje HPC |
| critic | `critic.md` | — |
| librarian | `librarian.md` | — |

Monitorowanie kolejki i przełączanie klastra opisuje `programmer.operator.md`: doklejasz do `programmer.md`, gdy zadanie tego wymaga, albo zlecasz samodzielnie innej instancji czytającej oba pliki.

## Limity modeli (dla operatora)

Agent nie przełącza sam modelu, który go napędza — gdy subskrypcja (Codex/Cursor/Claude/Antigravity) padnie na limit, czekasz na odnowienie albo uruchamiasz inną rolę/narzędzie ręcznie. Wyjątek: librarian przełącza źródło wyszukiwania w obrębie sesji (patrz `roles/librarian.md`), bez Twojej interwencji.

Harness w `orx agent spawn` to zamknięty zbiór (`claude-code`, `codex`, `cursor`, `antigravity`, `opencode`). Darmowy Google AI Studio = `opencode` + provider `google` + `GEMINI_API_KEY` (`opencode models` podaje aktualne id). Przydział ról: `model-assignment.md`.
