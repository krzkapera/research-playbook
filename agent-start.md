# Start sesji agenta

Przed pierwszą merytoryczną wiadomością:

1. Odczytaj `access-matrix.md`.
2. Odczytaj wyłącznie pliki z `common/` wymienione w macierzy oraz przekazany plik roli z `roles/`. Gdy rola odsyła do pliku domenowego — przeczytaj też jego.
3. Czytaj wyłącznie dokumenty wskazane w roli i w `access-matrix.md`.
4. Ustal slug hipotezy i/albo eksperymentu, odbiorcę i oczekiwany rezultat: najpierw w bieżącym komunikacie; gdy brak `project_id` — `orx projects`; przy znanym `project_id` — kanał `project` (`read_messages` ze `scope: "project"` albo `search_messages`, ai-crew-sync) oraz `orx project view <project_id>`.
5. Jeśli któregoś pola nadal nie da się ustalić, zapytaj krótko nadawcę albo zgłoś blokadę (brakujący kontekst nie domyślaj).
6. Dołącz do kanałów z briefu spawnu od razu (brief = zaproszenie) oraz według `common/communication.md` („Kanały"): zawsze `project`; kanał sluga hipotezy/eksperymentu tylko przy aktywnej roli w tym węźle; bez dołączania na zapas; zaproszenie innych na kanał = start rundy recenzji.
7. Przeczytaj opis węzła na poziomie zlecenia: przy znanym slug/id hipotezy — węzeł hipotezy (`orx exp desc` / `orx exp status`); przy znanym slug/id eksperymentu — wtedy węzeł eksperymentu; potem wskazane artefakty/logi (`orx logs`). Brief bez sluga/id — pomiń węzły `orx`. Tylko hipoteza — czytaj hipotezę, pomiń eksperyment.
8. Innych agentów szukaj przez `list_agents`. Potwierdź krótko: rola, cel, co i gdzie oddasz.
9. Gdy rola jest sprzeczna z dokumentem hipotezy, eksperymentu albo aktualną decyzją zespołu — zgłoś konflikt na właściwym kanale i czekaj.

Dokument spoza Twojego zakresu (zlecenie do niego odsyła): zapytaj o niego, zamiast czytać samodzielnie.

## Zasady pracy

- Kod jest narzędziem do badania, a nie celem samym w sobie.
- Sprawdzaj własne i cudze założenia.
- Hipoteza, eksperyment, implementacja, infrastruktura i interpretacja są rozdzielnymi rzeczami — nie mieszaj ich w jednej odpowiedzi bez oznaczenia poziomu.
- Kolejny krok wynika z aktualnych dowodów; planuj jeden mały krok naprzód.

## Poziom pracy

- Bez sluga: sprawa projektu lub nowa propozycja.
- Slug hipotezy, bez sluga eksperymentu: rozmowa o hipotezie.
- Slug hipotezy i sluga eksperymentu-dziecka: konkretny eksperyment.
- Identyfikator runu (`orx runs`): wykonanie jednego joba.
- Ścieżka artefaktu/logu: analiza konkretnego wyniku.

Gdy zadanie miesza poziomy, rozdziel odpowiedź: co jest decyzją na poziomie hipotezy, a co krokiem technicznym eksperymentu.
