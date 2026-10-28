# DEM – algebra rastrów

Ćwiczenia obejmują pełny proces rastrowej analizy terenu: od utworzenia syntetycznego modelu wysokościowego (DEM), przez analizy morfometryczne, po wielokryterialną ocenę przydatności terenu (fuzzy MCDA).

**Cel dydaktyczny:** przetwarzanie i analiza rastrów w NumPy, wizualizacja wyników w Matplotlib oraz odczyt i zapis danych georeferencyjnych za pomocą Rasterio. Samodzielne tworzenie funkcji Pythona do ponownego wykorzystania.

## Materiały

- [`01dem_create.md`](01dem_create.md) – generowanie DEM, georeferencja, zapis i odczyt GeoTIFF.
- [`02analizy1.md`](02analizy1.md) – gradient, nachylenie, ekspozycja, cieniowanie i warstwice.
- [`03analizy2.md`](03analizy2.md) – reklasyfikacja, funkcje przynależności fuzzy i rastrowa analiza wielokryterialna.

Dokumenty prowadzą przez kolejne etapy ćwiczeń. Szczegółowe polecenia, wzory i zadania znajdują się w odpowiednich plikach.

## Organizacja pracy

- Analizy wykonywane są w notebookach Jupyter.
- Funkcje powstają stopniowo podczas ćwiczeń; po sprawdzeniu trafiają do własnego pakietu `dem_tools`.
- Pakiet ma układ `src`, konfigurację `pyproject.toml` i jest instalowany w trybie edytowalnym (`pip install -e .`).
- Podstawowe biblioteki: `NumPy`, `Matplotlib`, `Rasterio`, `ipywidgets`.

## Dane

- DEM i warstwy wykorzystywane w analizach są syntetyczne.
- Sztuczna georeferencja umożliwia pracę z danymi przestrzennymi w metrach.
- Wartości i progi analizy przydatności są założeniami dydaktycznymi, a nie oceną rzeczywistych lokalizacji.
