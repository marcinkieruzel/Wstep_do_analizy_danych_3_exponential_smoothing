# Wygładzanie wykładnicze — dane o wypadkach drogowych w Polsce

Dane do zadań z wygładzania wykładniczego (Holt, Holt–Winters). Na razie tylko dane i ich pochodzenie.

| Plik | Okres | Krok | Wiersze | Źródło | Charakter danych |
|---|---|---|---|---|---|
| [dane/policja_informacja_dzienna.csv](dane/policja_informacja_dzienna.csv) | __ZAKRES_DZIENNY__ | dzień | __N_DZIENNY__ | KGP, „Informacja dzienna” (policja.pl) | wstępne, operacyjne |
| [dane/sewik_miesiecznie.csv](dane/sewik_miesiecznie.csv) | 2015-01 – 2025-12 | miesiąc | 132 | KGP, zbiór „Zdarzenia w ruchu drogowym” (dane.gov.pl) | ostateczne, z SEWiK |

Pobrano: 2026-10-24.

---

## 1. Dane dzienne — „Informacja dzienna” Policji

**Źródło:** Komenda Główna Policji, serwis policja.pl → Bazy danych → Informacja dzienna
<https://policja.pl/pol/form/1,Informacja-dzienna.html>

Tabela publikowana codziennie, stronicowana (`?page=1` … `?page=434` w dniu pobrania; najnowsze dni na stronie 1, 15 dni na stronę).
Nie ma eksportu CSV ani API, więc dane są zebrane ze stron HTML skryptem
[skrypty/pobierz_policja_informacja_dzienna.py](skrypty/pobierz_policja_informacja_dzienna.py).
Surowe strony (gzip) leżą w [dane/surowe/policja_informacja_dzienna/](dane/surowe/policja_informacja_dzienna/), więc CSV można odtworzyć bez sieci (`--offline`).

**Kolumny** (nazwy w nawiasach to oryginalne nagłówki tabeli):

| kolumna | oryginał |
|---|---|
| `data` | Data (RRRR-MM-DD) |
| `interwencje` | Interwencje |
| `zatrzymani_na_goracym_uczynku` | Zatrzymani na gorącym uczynku |
| `zatrzymani_poszukiwani` | Zatrzymani poszukiwani |
| `kierujacy_po_alkoholu` | Kierujący po spożyciu alkoholu |
| `wypadki` | Wypadki drogowe |
| `zabici` | Zabici w wypadkach |
| `ranni` | Ranni w wypadkach |

Do zadań z wypadkami liczą się `wypadki`, `zabici`, `ranni`. Pozostałe kolumny zostawiłem, bo nic nie kosztują
(np. `kierujacy_po_alkoholu` ma wyraźny rytm tygodniowy i może posłużyć jako drugi przykład).

__SEKCJA_JAKOSC_DZIENNA__

**Warunki wykorzystania:** policja.pl prosi o podanie źródła („W przypadku wykorzystania materiałów prosimy o podanie ich źródła: policja.pl” — <https://policja.pl/pol/wolnytekst/46616,dok.html>).
Do cytowania: *Komenda Główna Policji, „Informacja dzienna”, policja.pl, dostęp 24.10.2026.*

---

## 2. Dane miesięczne — SEWiK przez dane.gov.pl

**Źródło:** Komenda Główna Policji, zbiór „Zdarzenia w ruchu drogowym” na portalu dane.gov.pl, licencja **CC BY 4.0**
<https://dane.gov.pl/pl/dataset/834,zdarzenia-w-ruchu-drogowym>

Dane pochodzą z Systemu Ewidencji Wypadków i Kolizji (SEWiK). To dane **ostateczne**, te same co w rocznych raportach KGP „Wypadki drogowe w Polsce w … roku”.
Każdy rok to osobny zasób zbioru; skrypt [skrypty/pobierz_sewik_miesiecznie.py](skrypty/pobierz_sewik_miesiecznie.py) pobiera zasoby „statystyka ogólna — podział na miesiące” i skleja je w jeden szereg.
Oryginalne pliki leżą w [dane/surowe/dane_gov_pl_834_miesiace/](dane/surowe/dane_gov_pl_834_miesiace/).

| rok | id zasobu na dane.gov.pl | format |
|---|---|---|
| 2015 | 4233 (zbiorczy plik roku, arkusz „wg. miesięcy”) | XLS |
| 2016 | 5943 | CSV |
| 2017 | 5965 | CSV |
| 2018 | 16311 | CSV |
| 2019 | 24622 | CSV |
| 2020 | 29548 | CSV |
| 2021 | 38157 | CSV |
| 2022 | 47312 | CSV |
| 2023 | 56173 | CSV |
| 2024 | 66172 | CSV |
| 2025 | 1458662 | CSV |

**Kolumny:** `miesiac` (RRRR-MM), `wypadki`, `zabici`, `ranni`, `kolizje`.

**Kontrola:** dla każdego roku suma 12 miesięcy jest równa wierszowi „Podsumowanie” w pliku źródłowym (skrypt przerywa pracę, jeśli nie jest).
Sumy roczne 2016–2025 zgadzają się też z tabelą „Liczba wypadków drogowych oraz ich skutki w latach 2016–2025” w raporcie
[Wypadki drogowe w Polsce w 2025 roku](https://statystyka.policja.pl/download/20/473186/Wypadkidrogowe2025.pdf) (KGP, Biuro Ruchu Drogowego).

**Uwagi:**
- Wcześniejszych lat (przed 2015) w tym zbiorze nie ma. Starsze dane miesięczne są tylko w rocznych raportach PDF na statystyka.policja.pl.
- Pliki 2016–2025 są w kodowaniu cp1250, z separatorem `;` i różnią się nagłówkami (np. „Liczba Wypadkow” / „Liczba wypadków”); 2016 ma spacje jako separator tysięcy. Skrypt to normalizuje.
- Plik 2015 wymaga pakietu `xlrd` (`pip install xlrd`).

Do cytowania: *Komenda Główna Policji, „Zdarzenia w ruchu drogowym”, dane.gov.pl, CC BY 4.0, dostęp 24.10.2026.*

---

## Odtworzenie

```bash
python3 skrypty/pobierz_policja_informacja_dzienna.py   # ok. 20–30 min (434 strony, 1 s przerwy)
python3 skrypty/pobierz_sewik_miesiecznie.py            # kilka sekund; potrzebuje xlrd dla 2015
```

Oba skrypty pomijają pliki już pobrane do `dane/surowe/`. Żeby pobrać świeże dane dzienne, usuń najpierw `dane/surowe/policja_informacja_dzienna/`.
