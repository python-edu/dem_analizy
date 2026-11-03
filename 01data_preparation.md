# Generowanie systetycznych danych wejściowych

Rastry do wygenerowania:
- DEM
- mapy pokrycia terenu - 3 warstwy:
  - wartswa dróg (roads)
  - warstwa linii energetycznych (power lines)
  - warstwa - mapa pokrycia terenu (woda, las, łąki, ...)


## np.ndarray

- rozmiar tablicy rows x columns: 300 x 300
- typy danych: float32 (DEM), uint8 (pozostałe)

## Georeferencja
  - układ: WGS 84 / UTM 34N (`EPSG:32634`)
  - rozdzielczość: 10 m/piksel ($dx = 10 m$, $dy = 10 m$)
  - obszar: 3 × 3 km = **9 km²**
  - położenie syntetyczne: okolice Krakowa
  - lewy górny narożnik: `x0 = 423000`, `y0 = 5547500` [m]
  - georeferencja nie zmienia wysokości; wysokości nie mają rzeczywistego pionowego układu odniesienia


---

# Syntetyczny DEM

## Model matematyczny 

1. Model ogólny
$$
DEM(x,y) = offset + d_1(x,y) + d_2(x,y)
$$

  - `offset` – wysokość bazowa [m]
  - $d_1$, $d_2$ - modele wzgórza nr 1 i nr 2


2. Model matematyczny wzgórza

$$
d_i(x,y) = h_i \exp\left(-\frac{(x-x_i)^2 + (y-y_i)^2}{w_i^2}\right), \quad i=1,2
$$


  - `x1, y1, x2, y2` – środki wzgórz [piksele]
  - `h1, h2` – wysokości składowych wzgórz ponad poziomem bazowym [m]
  - `w1, w2` – parametry szerokości wzgórz [piksele]



## Przykład implementacji

W pakiecie `dem_tools` utwórz funkcję `dem_generator()`:

```python
def dem_generator(x1, y1, h1, w1, x2, y2, h2, w2, offset, size=300):
    if w1 <= 0 or w2 <= 0:
        raise ValueError("w1 and w2 must be positive")

    x = np.arange(size)
    y = np.arange(size)
    X, Y = np.meshgrid(x, y)

    dem1 = h1 * np.exp(-((X - x1)**2 + (Y - y1)**2) / w1**2)
    dem2 = h2 * np.exp(-((X - x2)**2 + (Y - y2)**2) / w2**2)

    return offset + dem1 + dem2
```


## Generowanie danych

Dane generowane są interaktywnie z wykorzystaniem modułu `ipywidgets` i jego funkcji `interactive()`.

Pozostałe funkcje:
  - funkcja główna `dem_tools.dem_generator()`
  - funkcje pomocnicze:
    - `dem_tools.show()` - wyświetla `DEM`
    - `generator_help()` - wywołuje `dem_generator()`, wyświetla wynik przez `show()`, zwraca DEM. Funkcja tworzona
    w notebooku, nie jest częścią pakietu `dem_tools`

```python
    x1=(30, 180, 5),     # domyślnie 105
    y1=(30, 180, 5),     # domyślnie 105
    h1=(40, 160, 5),     # domyślnie 100
    w1=(60, 180, 5),     # domyślnie 120

    x2=(120, 270, 5),    # domyślnie 195
    y2=(120, 270, 5),    # domyślnie 195
    h2=(0, 80, 5),       # domyślnie 40
    w2=(20, 100, 5),     # domyślnie 60

    offset=(0, 200, 10), # domyślnie 100
    size=widgets.fixed(300)
```
---


# Mapy pokrycia terenu

## scikit-image

Zaisntaluj scikit-image w środowisku kernela:

    ```bash
    python -m pip install -U scikit-image
    ```

Pakiet instalowany tylko na potrzeby ułatwieia generowania prostych elementów map.

## Drogi

- wygenerj tablice numpy (roads) wypełnioną wartościmi `0`, typ danych `uint8`
- użyj modułu `draw`: `import skimage.draw as draw`
- wygeneruj 3 drogi:
  - wygeneruj wiersze i columny używając: rows, cols = draw.line(...)
- przykład:

  ```python
  rows, cols = draw.line(185, 90, 150, 155)
  roads[rows, cols] = 1
  ```

## Linie energetyczne

Analogicznie jak drogi, wygeneruj tablicę `pow_line` przynajmniej z jedną linią energetyczną.


## Mapa pokrycia tereny

Wygeneruj tablicę `landcover`:
  - `uint8`
  - kilka typowych klas: woda, las, łąki, zabudowa, ...
  - rozmieść klasy uwzględniając DEM np. woda na płaskim terenie
  - użyj funkcji z `draw` np. line, polygon, elipse, disc itp.
  - przykład:

    ```python
    rr, cc = draw.ellipse(122, 155, 15, 25, rotation=np.pi/4)
    landcover[rr,cc] = 3  # woda
    ```

---


# Zapis GeoTIFF

Z wygenerowanych rastrów utwórz jedną tablicę w ukadzie `(rows, cols, bands)`.

## Parametry transformacji

Oblicz parametry transformacji afinicznej, wykorzystując `Affine`, napisz wrapper:

  ```python
  import rasterio.warp as warp
  
  def calc_transform(x0, y0, rotation, scale_x, scale_y, **kwargs):
      return (warp.Affine.translation(x0, y0) * warp.Affine.rotation(rotation)
              * warp.Affine.scale(scale_x, -scale_y))
  ```

## Write function

Napisz funckję niższego poziomu, zapisującą raster. Przykład implementacji:

  ```python
  def write_raster(ar: np.ndarray, out_file, driver,
                   width, height, count,
                   crs, transform, dtype, **kwargs):
          
      with rasterio.open(out_file,
                         mode='w',
                        driver=driver,
                        width=width,
                        height=height,
                        count=count,
                        crs=crs,
                        transform=transform,
                        dtype=dtype,
                        nodata=None) as dst:
          if ar.ndim == 2:
              dst.write(ar, 1)
          else:
              for i in range(count):
                  dst.write(ar[..., i], i + 1)
  
      return 1
  ```

## Save data

Napisz funkcję obsługującą cały cykl zapisu rastra - wzór implementacji:

```python

def save(ar, ...):
    # transform
    transform = calc_transform(...)

    # Obsługa typu danych
    # ar astype ...

    # obsługa arguemntów zapisu na podstawie `ar`
    # dtype, count, height, width, ... 

    # zapis
    write_raster(...)

    # komunikat o udanym / nieudanym zapisie
    print(message)

```

---


# Odczyt danych do analiz

Napisz funkcję odczytującą raster - przykład:

```python
def read(pth: str | Path):
    pth = str(pth)

    with rasterio.open(pth) as src:
        im = src.read()
        if im.shape[0] == 1:
            im = im[0]
        else:
            im = im.transpose(1, 2, 0)

        return im
```


## Przydatne funkcje - przykłady implementacji.

### find_raster

```python
def find_raster(pth='.'):
    pths = Path(pth).rglob('*.tif')
    return list(pths)
```

### info()

```python
def info(*pths: str):
    tmpl = '''
    file:      {file}
    count:     {count:<12}\tshape:   {shape}
    dtype:     {dtype:<12}\tnodata:  {nodata}
    min:       {min:<12.2f}\tmax:    {max:<12.2f}\tstd: {std:.2f}
    crs:       {crs}
    bounds:    {bounds}
    res:       {res}
    transform: {transform}
    '''

    for pth in pths:
        with rasterio.open(pth) as src:
            metadata = src.meta.copy()
            ar = np.ma.masked_invalid(src.read(masked=True))

            metadata['shape'] = src.shape
            metadata['bounds'] = src.bounds
            metadata['res'] = src.res
            metadata['min'] = ar.min()
            metadata['max'] = ar.max()
            metadata['std'] = ar.std()

        metadata['file'] = Path(pth).name
        print(tmpl.format(**metadata))
        print()

```
