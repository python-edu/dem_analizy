# Temat

> Analizy morfometryczne syntetycznego DEM

---

# Odczyt DEM

- otwórz `dem_utm.tif` za pomocą `rasterio`
- odczytaj pierwszy kanał do tablicy NumPy
- pobierz `crs`, `transform`, `res`, `bounds`
- wyznacz `extent` za pomocą `plotting_extent()`
- wyświetl DEM funkcją `dem_show()` z modułu `dem_tools`
- zapisz rozdzielczość: `dx`, `dy` [m/piksel]

> Do obliczeń pochodnych używaj rozdzielczości w metrach, nie numerów pikseli.

---

# Gradient wysokości

- gradient – zmiana wysokości w kierunkach X i Y
- `np.gradient()` – pochodne obliczane numerycznie
- kolejność wyników dla rastra 2D: **wiersze, kolumny**

$$
g_s = \frac{\partial z}{\partial s},\qquad
 g_x = \frac{\partial z}{\partial x}
$$

- `x` – kierunek na wschód
- `s` – kierunek w dół tablicy (na południe dla rastra north-up)
- `np.gradient(dem, dy, dx)` → `g_s, g_x`
- wyświetl obie składowe gradientu

Funkcja do `dem_tools.py`:

```python
def dem_gradient(dem, dx, dy):
    pass
```

---

# Nachylenie terenu – slope

- oblicz nachylenie na podstawie składowych gradientu
- porównaj wartości w stopniach i procentach

$$
g = \sqrt{g_x^2 + g_s^2}
$$

$$
\mathrm{slope}_{\deg} = \arctan(g)\,\frac{180}{\pi}
$$


$$
slope_\text{proc} = 100g
$$


- `np.hypot()`, `np.arctan()`, `np.degrees()`
- wyświetl mapę nachylenia i histogram
- wskaż obszary o największym nachyleniu

Funkcja do `dem_tools.py` (wynik w stopniach):

```python
def dem_slope(dem, dx, dy):
    pass
```

---

# Ekspozycja terenu – aspect

- aspect – azymut kierunku **największego spadku** terenu
- azymut: `0°` – N, `90°` – E, `180°` – S, `270°` – W
- `np.arctan2()` – wyznaczanie kąta z dwóch składowych gradientu

Dla rastra **north-up**, gdy `g_s` jest pochodną w kierunku południowym:

$$
\mathrm{aspect} = \bigl(\operatorname{degrees}(\operatorname{atan2}(-g_x,\,g_s)) + 360\bigr)\bmod 360
$$

- tereny płaskie (`g = 0`) – brak określonej ekspozycji; użyj maski
- wyświetl mapę ekspozycji (skala barw dla kierunków)

Funkcja do `dem_tools.py` (wynik w stopniach):

```python
def dem_aspect(dem, dx, dy):
    pass
```

---

# Cieniowanie – hillshade

- symulacja oświetlenia powierzchni DEM
- `matplotlib.colors.LightSource`
- parametry: azymut i wysokość źródła światła
- wynik: raster intensywności oświetlenia w zakresie `0–1`

Funkcja do `dem_tools.py`:

```python
def dem_hillshade(dem, dx, dy, azimuth=315, altitude=45):
    pass
```

- porównaj oświetlenie z różnych kierunków: `315°`, `90°`, `180°`
- zmień wysokość źródła światła: `30°`, `60°`
- wyświetl DEM i hillshade obok siebie

---

# Warstwice – contours

- warstwice – linie jednakowej wysokości
- `plt.contour()` – rysowanie izolinii
- `levels` – poziomy wysokości warstwic
- `extent` i `origin="upper"` – zgodność z położeniem rastra

- narysuj warstwice co `10 m` i `20 m`
- dodaj etykiety wysokości (`clabel()`)
- nałóż warstwice na obraz DEM

> Na tym etapie wystarczy kod w notebooku; osobna funkcja nie jest konieczna.

---

# Moduł `dem_tools.py`

- dodaj: `dem_gradient()`, `dem_slope()`, `dem_aspect()`, `dem_hillshade()`
- wykorzystaj istniejące: `dem_show()`, `save_dem()`
- rozszerz `dem_show()` o opcjonalną mapę barw (`cmap`) i opis skali (`label`)
- przetestuj funkcje na DEM z notebooka 1
- sprawdź wyniki przy innej rozdzielczości przestrzennej (`dx`, `dy`)

> Funkcje obliczeniowe zwracają tablice; wyświetlanie i zapis wykonuj osobno.
