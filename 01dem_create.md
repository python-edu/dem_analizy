# Generowanie syntetycznego DEM

## 1. DEM

- Numeryczny model terenu (NMT / DTM): raster wysokości terenu
- powierzchnia bazowa i dwa wzgórza
- rozmiar: 300 × 300 pikseli
- wynik generatora: tablica NumPy `float64`

## 2. Generator DEM

Napisz funkcję `dem_generator()`:

```python
def dem_generator(x1, y1, h1, w1, x2, y2, h2, w2, offset):
    # np.arange(), np.meshgrid(), np.exp()
    return dem
```

### Model matematyczny

$$
d_i(x,y) = h_i \exp\left(-\frac{(x-x_i)^2 + (y-y_i)^2}{w_i^2}\right), \quad i=1,2
$$

$$
DEM(x,y) = offset + d_1(x,y) + d_2(x,y)
$$

- `x1, y1, x2, y2` – środki wzgórz [piksele]
- `h1, h2` – wysokości składowych wzgórz ponad poziomem bazowym [m]
- `w1, w2` – parametry szerokości wzgórz [piksele]
- `offset` – wysokość bazowa [m]
- `np.meshgrid()` – siatka współrzędnych pikselowych
- `np.exp()` – profil wysokości wzgórza

## 3. ipywidgets

Interaktywne ustawianie parametrów DEM:

- `generator_help()` – wywołuje `dem_generator()`, wyświetla wynik przez `dem_show()`, zwraca DEM
- `widgets.interactive()` – suwaki parametrów
- `wd.result` – ostatni wygenerowany DEM (po ustawieniu suwaków)
- `generator_help()` pozostaje w notebooku; `dem_generator()` i `dem_show()` w module `dem_tools.py`

## 4. Zapis GeoTIFF

- układ: WGS 84 / UTM 34N (`EPSG:32634`)
- rozdzielczość: 10 m/piksel
- obszar: 3 × 3 km = **9 km²**
- położenie syntetyczne: okolice Krakowa
- lewy górny narożnik: `x0 = 423000`, `y0 = 5547500` [m]
- `from_origin()` – transformacja afiniczna z położenia i rozdzielczości
- `save_dem()` – zapis GeoTIFF (dane `float32`, CRS, transformacja)
- georeferencja nie zmienia wysokości; wysokości nie mają rzeczywistego pionowego układu odniesienia

```python
save_dem(dem, "dem_utm.tif", x0=423000, y0=5547500)
```

## 5. Odczyt i wizualizacja GeoTIFF

- `rasterio.open()` – otwarcie pliku
- `src.read(1)` – pierwszy kanał rastra → tablica NumPy
- `src.crs`, `src.res`, `src.bounds` – georeferencja
- `plotting_extent(src)` – granice rastra dla `imshow()`
- `dem_show(dem, extent=extent)` – obraz z osiami UTM

```python
from rasterio.plot import plotting_extent

with rasterio.open("dem_utm.tif") as src:
    dem = src.read(1)
    extent = plotting_extent(src)

dem_show(dem, extent=extent)
```


Parametr	Zakres	Krok
x1, y1, x2, y2	0–299	10
h1, h2	0–120	10
w1, w2	20–100	5
offset	0–200	10
size	300	stały
