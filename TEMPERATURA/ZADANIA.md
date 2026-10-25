# Zadania — temperatura (Warszawa-Okęcie)

**Dane:** `warszawa_okecie_temp_dobowa.csv`: średnia dobowa temperatura powietrza [°C] na stacji Warszawa-Okęcie, od 1.01.1951 do 30.09.2026 (IMGW-PIB, danepubliczne.imgw.pl).
Kolumna `zrodlo` mówi, z której serii IMGW pochodzi wiersz: `klimat` do 1965 r., potem `synop`.

Utwórz w tym folderze notatnik `temperatura.ipynb`. Odpowiedzi na pytania pisz w komórkach Markdown pod kodem.

## Zadania

1. **Wczytanie.** Wczytaj CSV przez pandas tak, żeby indeksem była kolumna `data` jako daty. Sprawdź, czy są braki i czy każdy dzień występuje dokładnie raz.
2. **Agregacja.** Zamień dane dzienne na średnie miesięczne (`resample("MS").mean()`). Narysuj okres od 1991 r.
3. **Holt bez sezonowości.** Na miesiącach 1991–2023 dopasuj model Holta i zrób prognozę na 2024-01 – 2026-09. Narysuj ją. Dlaczego wychodzi tak źle?
4. **Holt–Winters.** Na tych samych danych dopasuj model z sezonowością roczną (`seasonal_periods=12`). Policz MAE na okresie testowym i porównaj je z „klimatologią”, czyli średnią temperaturą danego miesiąca z lat 1991–2023.
5. **Addytywnie czy multiplikatywnie?** Spróbuj `seasonal="mul"`. Co się dzieje i dlaczego dla temperatury w °C ten wariant nie ma sensu?
6. **Trend ocieplenia.** Policz średnie roczne (`resample("YE").mean()`) i pomiń niepełny 2026 r. Dopasuj Holta i zrób prognozę na lata 2026–2030. O ile stopni na rok rośnie prognoza? Porównaj z różnicą między średnią z lat 1951–1980 a średnią z lat 1996–2025.
7. *(dla chętnych)* Odłóż lata 2016–2025 jako test i sprawdź, czy Holt prognozuje je lepiej niż proste wygładzanie wykładnicze bez trendu (`ExponentialSmoothing(train)`).

## Podpowiedzi

```python
import pandas as pd
from statsmodels.tsa.holtwinters import ExponentialSmoothing

holt = ExponentialSmoothing(train, trend="add").fit()
hw = ExponentialSmoothing(train, trend="add", seasonal="add", seasonal_periods=12).fit()
prognoza = hw.forecast(len(test))
mae = (test - prognoza).abs().mean()
```

- Holta też licz przez `ExponentialSmoothing(..., trend="add")`. Klasa `Holt` w statsmodels 0.15 kończy się błędem, jeśli nie podasz `initialization_method="estimated"`.
- Średnie roczne zostaw z indeksem dat, który daje `resample`. Jeśli zamienisz go na same numery lat, `forecast()` nie zadziała.
- Pakiety zainstalujesz w głównym folderze repozytorium: `pip install -r requirements.txt`.
