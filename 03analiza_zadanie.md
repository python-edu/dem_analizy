# Wybór lokalizacji parku miejskiego — Boolean i Fuzzy

## Cel

Wyznacz obszary przydatne do lokalizacji nowego parku miejskiego w Krakowie:
  - wykonaj analizę rastrową metodami **Boolean** (twarda) i **Fuzzy** (rozmyta)
  - porównaj wyniki

---


## Dane

Dane zapisane są w plikach `urban_atlas_team_id.tif`, gdzie id = 01, 02, ... Nr `id` odpowiada numerowi zespołu.
Plik zawierają dwa kanały:

1. **Urban Atlas** — klasy użytkowania i pokrycia terenu: [legenda Table2](https://library.land.copernicus.eu/products/Urban_Atlas_CLMS_UA2021_LULC_PUM_v1.html?utm_source#:~:text=Table%202%3A%20UA%20LC/LU%20nomenclature%20(in%20bold%2C%20classes%20used%20in%20the%20products%2C%20without%20any%20further%20subdivision))
2. **Copernicus DEM GLO-30** — model wysokościowy (DSM).

Rozdzielczość rastra: **10 m**, układ współrzędnych: **EPSG:3035**.

---


## Zadania

### 1. Przygotowanie danych

1. Odczytaj oba kanały rastra.
2. Na podstawie Urban Atlas wyznacz maski: zabudowy mieszkaniowej, istniejących terenów zieleni oraz dróg.
3. Oblicz rastry odległości od tych trzech kategorii w metrach.
4. Na podstawie modelu wysokościowego oblicz nachylenie (*slope*, w stopniach) i ekspozycję (*aspect*, w stopniach).


### 2. Analiza Boolean

  - wyznacz piksele spełniające **jednocześnie** następujące warunki:

| Kryterium | Warunek |
|---|---|
| Odległość od zabudowy mieszkaniowej | ≤ 500 m |
| Odległość od istniejącej zieleni | ≥ 300 m |
| Odległość od dróg | ≤ 300 m |
| Nachylenie | ≤ 10° |
| Ekspozycja | od 135° do 225° (SE–S–SW) |

  - połącz warunki operatorem logicznym `AND` i `OR`, zastosuj maskę obszarów dopuszczonych.


### 3. Analiza Fuzzy

  - zamień każde z pięciu kryteriów na raster stopnia przydatności od **0 do 1**.
  - zastosuj proste liniowe funkcje przynależności:

| Kryterium | Funkcja przynależności
|---|---|
| Odległość od zabudowy | malejąca |
| Odległość od zieleni | rosnąca |
| Odległość od dróg | malejąca |
| Nachylenie | malejąca |
| Ekspozycja | do wyboru: twarda maska azymutu lub fuzzy (własny dobór funkcji)|

  - w analize Fuzzy połącz kryteria czterema metodami:
    - średnia
    - średnia ważona
    - `AND` - minimum
    - `OR` - maximum
  - zastosuj tę samą maskę obszarów dopuszczonych co w analizie Boolean.


### 4. Porównanie wyników

1. Wyświetl mapę Boolean i mapę Fuzzy.
2. Oblicz powierzchnię obszarów spełniających kryteria Boolean.
3. Dla Fuzzy oblicz powierzchnie pikseli o przydatnościach: **≥ 0,5** oraz **≥ 0,7**.
4. Porównaj rozmieszczenie i powierzchnie obszarów wybranych obiema metodami.
5. Wyjaśnij, jak ostre progi Boolean i stopniowanie ocen Fuzzy wpływają na wyniki.

---


## Podziała na zespoły

| Zespół | Obszar |
|---:|---|
| 1 | Kraków – Centrum |
| 2 | Bronowice |
| 3 | Prądnik Biały |
| 4 | Prądnik Czerwony |
| 5 | Nowa Huta |
| 6 | Mistrzejowice |
| 7 | Czyżyny |
| 8 | Płaszów |
| 9 | Podgórze Duchackie |
| 10 | Borek Fałęcki |
| 11 | Dębniki – Zakrzówek |
| 12 | Tyniec |
| 13 | Wieliczka |
| 14 | Skawina |
