# Zadania — wypadki drogowe (SEWiK, dane miesięczne)

**Dane:** `sewik_miesiecznie.csv`: miesięczne liczby wypadków, zabitych, rannych i kolizji w Polsce, od 2015-01 do 2025-12 (132 miesiące).
Dane ostateczne Komendy Głównej Policji z systemu SEWiK, zbiór „Zdarzenia w ruchu drogowym” na dane.gov.pl, licencja CC BY 4.0.

Utwórz w tym folderze notatnik `wypadki.ipynb`. Odpowiedzi na pytania pisz w komórkach Markdown pod kodem.

## Zadania

1. **Wczytanie.** Wczytaj CSV przez pandas tak, żeby `miesiac` był indeksem dat, i ustaw częstotliwość miesięczną (`asfreq("MS")`). Narysuj `wypadki` i `kolizje`.
2. **Trend i sezonowość.** Policz roczne sumy wypadków. W którym roku szereg spada najmocniej? Które miesiące mają zwykle najwięcej, a które najmniej wypadków?
3. **Holt i Holt–Winters.** Trening: 2015–2023, test: 2024–2025. Dopasuj Holta oraz Holta–Wintersa (`seasonal_periods=12`) z sezonowością addytywną i multiplikatywną. Policz MAE i MAPE na teście.
4. **Punkt odniesienia.** Policz MAE prognozy naiwnej sezonowej: na 2024 i 2025 r. powtórz wartości z 2023 r. Który model wygrywa? Wyjaśnij wynik, patrząc na wykres z zadania 1.
5. **Tłumiony trend i okno danych.** Powtórz zadanie 3 z `damped_trend=True`, a potem z treningiem tylko na latach 2021–2023. Co bardziej poprawia prognozę?
6. **Ile wypadków „zabrała” pandemia?** Wytrenuj Holta–Wintersa na latach 2015–2019 i zrób prognozę na 2020 r. Porównaj sumę prognozy z faktyczną liczbą wypadków w 2020 r.
7. *(dla chętnych)* Najlepszym modelem z zadań 3–5 zrób prognozę na 2026 r., trenując na wszystkich danych. Ile wypadków wychodzi łącznie i jak to się ma do 2025 r.?

## Podpowiedzi

```python
import pandas as pd
from statsmodels.tsa.holtwinters import ExponentialSmoothing

holt = ExponentialSmoothing(train, trend="add").fit()
hw = ExponentialSmoothing(train, trend="add", seasonal="mul", seasonal_periods=12).fit()
prognoza = hw.forecast(24)
mae = (test - prognoza).abs().mean()
mape = ((test - prognoza) / test).abs().mean() * 100
```

- Holta też licz przez `ExponentialSmoothing(..., trend="add")`. Klasa `Holt` w statsmodels 0.15 kończy się błędem, jeśli nie podasz `initialization_method="estimated"`.
- Wycinanie po latach działa na indeksie dat: `y.loc[:"2023"]`, `y.loc["2024":]`.
- Potrzebne pakiety: `pip install pandas statsmodels matplotlib jupyterlab`.
