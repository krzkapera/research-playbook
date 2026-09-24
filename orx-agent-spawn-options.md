# `orx agent spawn` — dostępne opcje

Zebrane przez inspekcję binarki `orx` na lightsail (`strings $(which orx)`).
Data: 2026-09-24

---

## Składnia

```
orx agent spawn [OPTIONS] [TASK]
```

| Opcja | Opis |
|---|---|
| `[TASK]` | Treść zadania (self-contained brief; helper nie widzi bieżącego transkryptu) |
| `--stdin` | Czytaj zadanie ze stdin zamiast z argumentu (dla długich briefów) |
| `--title <TITLE>` | Nazwa sesji w sidebarze (domyślnie: auto-generated) |
| `--harness <HARNESS>` | Harness helpera (domyślnie: harness bieżącej sesji) |
| `--model <MODEL>` | Model helpera (domyślnie: model bieżącej sesji) |
| `--permission-mode <MODE>` | Tryb uprawnień helpera (dodane 2026-09-24, patrz niżej) |
| `--reasoning-level <LEVEL>` | Poziom reasoningu helpera (dodane 2026-09-24, patrz niżej) |
| `--service-tier <TIER>` | Tier/prędkość helpera, jeśli harness go ma (dodane 2026-09-24) |
| `--plan-mode` | Startuj helpera w trybie Plan — tylko na harnessach aktywujących Plan komendą (Codex, OpenCode), nie permission mode (Claude) (dodane 2026-09-24) |
| `--no-wake` | Nie wznawiaj tej sesji gdy helper skończy |
| `--no-telemetry` | Wyłącz anonimowe analytics dla tego uruchomienia |
| `-h, --help` | Pomoc |

> `--permission-mode`/`--reasoning-level`/`--service-tier` domyślnie dziedziczą po rodzicu (gdy harness się nie zmienia) albo spadają do domyślnej wartości harnessa — dokładnie jak `--model`. Podanie flagi jawnie zawsze wygrywa. `--permission-mode`/`--service-tier` są walidowane względem wybranego harnessa (błąd `invalid ... for selected harness`, jeśli wartość nie pasuje); `--reasoning-level` nie jest walidowany z góry — nieznana/nieaktualna wartość jest po prostu ignorowana przy starcie tury. `--plan-mode` nigdy nie dziedziczy (zawsze `false`, chyba że podana jawnie) i jest walidowana osobno (błąd `this harness activates Plan through permissions` na harnessach bez trybu komendowego).
>
> To komplet — sprawdzone w `ui/src/components/ModelPicker.tsx` i `ui/src/api.ts`: UI nie ma żadnego generycznego/dowolnego mechanizmu na "custom parametry", tylko te cztery ustalone osie. To, co wygląda jak "wiele różnych parametrów zależnie od modelu", to te same 4 osie z innym słownikiem dopuszczalnych wartości per model (np. inne `reasoningLevel` dla Claude vs Codex), nie dodatkowe parametry.

> **Uwaga**: `orx agent spawn` jest dostępne wyłącznie wewnątrz sesji agenta działającej przez `orx up`.

---

## `--harness` — dostępne wartości

| ID harnessa | Odpowiadający agent |
|---|---|
| `antigravity` | Antigravity (Google Deepmind) |
| `claude-code` | Claude Code (Anthropic) |
| `codex` | Codex (OpenAI) |
| `cursor` | Cursor |
| `opencode` | OpenCode |

---

## `--model` — znane wartości (wyekstrahowane z binarki)

### Antigravity / Gemini
| Model |
|---|
| `gemini-3.8-flash-low` (zidentyfikowany w binary jako `gemini-3.8-flash-lowAntigravity`) |

> Antigravity prawdopodobnie przyjmuje też inne Gemini modele. Dokładna lista zależy od konfiguracji backendu.

### Claude Code (`claude-code`)
| Model |
|---|
| `claude-opus-4-8` |
| `claude-sonnet-5` |
| `claude-fable-5` |
| `claude-sonnet-4-5` |
| `claude-haiku-4-5` |

Zidentyfikowane w binary: `claude-fable-5`, `claude-sonnet-5`, `claude-opus-4-8`, `claude-haiku-4-5`, `claude-sonnet-4-5`.

### Codex (OpenAI)
Modele konfigurowane przez `model_provider` + `model_catalog_json` (dynamicznie przez API).
Binary ujawnia pola: `model_reasoning_effort`, `supportedReasoningEfforts`, `defaultReasoningEffort`.

### Cursor / Grok
Modele Cursor są konfigurowane dynamicznie przez aplikację Cursor.

---

## Poziom reasoningu (`reasoningLevel`)

Parametr **nie jest** bezpośrednią flagą CLI `orx agent spawn` — jest częścią wewnętrznego protokołu między `orx` a harnesem.

### Architektura

```
orx agent spawn --harness X --model Y
       ↓
  wewnętrzny ORX:  { harness, model, permissionMode, reasoningLevel }
       ↓
  harness dostaje reasoningLevel z defaultReasoningLevel(harness, model)
```

Funkcja JS w binarce: `Byn(e,n)` = `{ harness: e.id, model: n, permissionMode: e.options?.defaultPermissionMode, reasoningLevel: xb(e,n).defaultId }`

### Znane wartości `reasoningLevel`

Wyekstrahowane ze stringów binarki (posortowane według częstości wystąpień):

| Wartość | Uwagi |
|---|---|
| `none` | Najczęstsza — brak rozszerzowanego reasoningu |
| `auto` | Automatyczny wybór przez model |
| `low` | Niski poziom |
| `normal` | Standardowy |
| `max` | Maksymalny |
| `high` | Wysoki (rzadka) |

### Dla Codex — `reasoning_effort`

Codex używa osobnego pola `model_reasoning_effort` (przekazywanego przez env var do app-serwera Codexa):

| Wartość |
|---|
| `low` |
| `medium` |
| `high` |
| `auto` |

Pola binary: `supportedReasoningEfforts`, `defaultReasoningEffort`, `serviceTier`, `effort`

### Jak ustawić reasoning level?

- **Przez ORX CLI**: od 2026-09-24 jest bezpośrednia flaga `--reasoning-level` w `orx agent spawn` (patrz `agent-start.md`/`common/communication.md` — dodane w tym forku, `src/commands/agent.rs`, `spawn()`; mirroruje walidację `commands/up.rs`'s `create_chat_session`).
- **Przez UI**: reasoning level ustawiany przez selektor w interfejsie `orx up` przy wyborze modelu.
- **Przez konfigurację harnessa**: `reasoningLevels` i `defaultReasoningLevel` są polami w deskryptorze harnessa (serwowanym przez `orx serve` / `orx up`) — to źródło dopuszczalnych wartości, `orx agent spawn --reasoning-level` samo ich nie listuje.

---

## `permissionMode` — znane wartości

Pole towarzyszące `reasoningLevel` w protokole wewnętrznym:

| Wartość | Opis |
|---|---|
| `ask` | Pytaj przed komendami wymagającymi podwyższonych uprawnień |
| `manual` | Ręczne zatwierdzanie |
| `accept-edits` / `acceptEdits` | Automatycznie akceptuj edycje |
| `bypass` / `bypassPermissions` | Pomiń sandbox/approval prompts |
| `approve-for-me` | Codex automatycznie zatwierdza requesty |
| `full-access` | Pełny dostęp, bez sandboxa |

Błąd przy nieprawidłowej wartości: `"invalid permission mode for selected harness"`

---

## Powiązanie z `model-assignment.md`

Gotowe komendy spawnu (harness, model, permission, reasoning, `--no-wake`) są w tabeli w `model-assignment.md`. Tu tylko słownik flag; przy spawnie bierz komendę stamtąd.


---

## Źródła

- `orx agent spawn --help` via `ssh lightsail`
- `strings $(which orx)` — inspekcja binarki Electron app na lightsail
- Wyekstrahowany JS: fragment funkcji `Byn`, `$yn`, `xb` z embeddowanego bundle
