# Zadania — przestępczość (dane dzienne Policji)

**Dane:** `policja_informacja_dzienna.csv`: dzienne liczby z „Informacji dziennej” Komendy Głównej Policji (policja.pl), od 1.12.2008 do 8.10.2026.
Kolumny do zadań: `zatrzymani_na_goracym_uczynku`, `zatrzymani_poszukiwani`, `kierujacy_po_alkoholu`, `interwencje` (ta dopiero od 2018 r.).

Utwórz w tym folderze notatnik `przestepczosc.ipynb`. Odpowiedzi na pytania pisz w komórkach Markdown pod kodem.

## Zadania

1. **Wczytanie.** Wczytaj CSV przez pandas tak, żeby indeksem była kolumna `data` jako daty. Ile jest wierszy i jaki okres obejmują dane?
2. **Braki.** W szeregu brakuje kilkudziesięciu dni. Uzupełnij kalendarz (`asfreq("D")`), policz, ile dni doszło, i wypełnij luki interpolacją.
3. **Rytm tygodnia.** Weź `zatrzymani_poszukiwani` od 2023-01-01. Narysuj wykres i policz średnią dla każdego dnia tygodnia. Które dni są najniższe? Przyjrzyj się też pojedynczym skokom na wykresie: skąd mogą się brać?
4. **Holt.** Ostatnie 8 tygodni (56 dni) odłóż jako zbiór testowy. Na pozostałych danych dopasuj model Holta (poziom + trend), zrób prognozę na 56 dni i policz MAE.
5. **Holt–Winters.** Dopasuj model z sezonowością tygodniową (`seasonal_periods=7`), raz addytywną, raz multiplikatywną. Porównaj MAE z zadaniem 4 i narysuj prognozy na tle danych testowych.
6. **Punkt odniesienia.** Policz MAE prognozy naiwnej sezonowej: przez 8 tygodni powtarzaj ostatni tydzień z treningu. Czy Holt–Winters ją bije?
7. **Parametry.** Wypisz α, β, γ z najlepszego modelu. Co mała wartość γ mówi o tym, jak zmienia się rytm tygodnia w czasie?
8. *(dla chętnych)* Powtórz zadania 4–6 dla `kierujacy_po_alkoholu`. Czy kolejność modeli jest taka sama?

## Podpowiedzi

```python
import pandas as pd
from statsmodels.tsa.holtwinters import ExponentialSmoothing

holt = ExponentialSmoothing(train, trend="add").fit()
hw = ExponentialSmoothing(train, trend="add", seasonal="add", seasonal_periods=7).fit()
prognoza = hw.forecast(56)
mae = (test - prognoza).abs().mean()
hw.params  # smoothing_level = α, smoothing_trend = β, smoothing_seasonal = γ
```

- Holta też licz przez `ExponentialSmoothing(..., trend="add")`. Klasa `Holt` w statsmodels 0.15 kończy się błędem, jeśli nie podasz `initialization_method="estimated"`.
- Dzień tygodnia z indeksu: `y.index.day_of_week` (0 = poniedziałek).
- Pakiety zainstalujesz w głównym folderze repozytorium: `pip install -r requirements.txt`.
