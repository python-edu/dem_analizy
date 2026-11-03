# Algebra rastrów

Ćwiczenia obejmują pełny proces rastrowej analizy terenu: od utworzenia danych syntetycznych, rastrów pochodnyc,
po wielokryterialną ocenę przydatności terenu (fuzzy MCDA).

**Cel dydaktyczny:** przetwarzanie i analiza rastrów w NumPy, wizualizacja wyników w Matplotlib oraz odczyt i zapis
danych georeferencyjnych za pomocą Rasterio. Samodzielne tworzenie funkcji Pythona do ponownego wykorzystania.

---


# Schemat ogólny laboratorium

## Projekt

1. Python:
  - nowy pakiet: `dem_tools`
  - układ `src`
  - `pyproject.toml`

2. Jupyter:
  - instalacja zależności w środowisku używanego kernela: numpy, matplotlib, rasterio, ipywidgets, scikit-image
  - scikit-image: `python -m pip install -U scikit-image`
  - instalacja (editable mode) pakietów `raster_tools`, `dem_tools`


## Przygotowanie danych

1. Utowrzenie generatora sztucznego `DEM`:
  - wygenerowanie DEM

4. Wygenerowanie rastrów pokrycia terenu:
  - drogi, linie energetyczne, mapa pokrycia terenu

5. Zapis rastrów do plik `.tif`:
  - jeden raster z wieloma kanałami: DEM, drogi, linie en., mapa pokrycia terenu
  - nadanie georeferencji i zapis na dysk


## Analizy

  1. Generowanie mapy nachyleń i azymutów (aspect)
  2. Reklasyfikacja, kryteria wyboru lokalizacji

