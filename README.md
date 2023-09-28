# Lietuvos miškų kirtaviečių NDVI vertinimo projektas

- Projektas vykdomas mokymosi tikslais, kaip profesinės praktikos užduotis.
- Projektas nėra oficialus ir nėra susijęs su šiame aprašyme minimomis organizacijomis.
- Projekto pagrindinis tikslas yra ištirti kaip NDVI reikšmės atspindi miško kirtavietės pokyčius per pasirinktą laiko tarpą.
- Projektas nuo 2023-10-01 bus aktyviai nebetęsiamas, bet gali būti kartais papildomas.

**Svarbu! Darbo autorius negarantuoja duomenų tikslumo ir tinkamo pateiktų programų veikimo!**

---

## Trumpas darbo proceso aprašymas

Darbo procesas:

1. Atrinkti kirtaviečių plotai.
2. Iš atrinktų kirtaviečių plotų atsitiktinai pasirinkti penki plotai.
3. Pasirinktuose plotuose apskaičiuotos vidutinės mėnesio NDVI (ir kitos) reikšmės laikotarpiu 2017-06-01 - 2022-08-01.
4. Apskaičiuotos reikšmės pavaizduotos grafikuose.

---

## Duomenų šaltiniai

- Kirtaviečių duomenys atrinkti pasitelkiant Valstybinės miškų tarnybos viešai pasiekiamas paslaugas, prieinamas per M-GIS. Miško kirtaviečių plotai atrinkti pagal 2019 - 2020 m. Sentinel palydovo identifikuotus plynus miško kirtimo plotus, kurie taip pat pasiekiami per M-GIS. [M-GIS nuoroda.](https://kadastras.amvmt.lt/portal/apps/webappviewer/index.html?id=42967a7ae33848a6ad8a577a70307607)
- Pagrindiniai palydoviniai duomenys naudoti NDVI ir kitiems skaičiavimams atlikti yra Sentinel-2 MSI: MultiSpectral Instrument, Level-2A duomenys. [Earth Engine Data Catalog nuoroda.](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR#description)
- Kiti palydoviniai duomenys naudoti skaičiavimų tikslumui palyginti yra MODIS Terra Daily NDVI. [Earth Engine Data Catalog nuoroda.](https://developers.google.com/earth-engine/datasets/catalog/MODIS_MOD09GA_006_NDVI#description)

---

## Naudota programinė įranga

- Qgis.
- Python 3.
- Google Earth Engine (GEE) Python API.
- Jupyter Notebook.

---

## Atrinktų kirtaviečių duomenys

Duomenys gali būti pasiekiami [aplanke geojson.](./geojson)

**Svarbu! Atliekant skaičiavimus duomenis patogu įsikelti per GEE Code Console kaip shapefile (.shp).**

Atributinės lentelės stulpelių reikšmės:

- fid - automatiškai sugeneruotas objekto numeris.
- km - kirtavietės aptikimo metai.
- amz - vidutinis medžių amžius teritorijoje (m.).
- h - vidutinis medžių aukštis teritorijoje (m).
- geokodas - sklypo kuriame yra kirtavietė geokodas.
- vmr - vyraujanti medžių rūšis, be lietuviškų raidžių (pvz. a yra ąžuolas).
- area - teritorijos plotas ($m^2$).
- perimeter - teritorijos perimetras (m).

Papildomi duomenys (selected_objects_areas_wgs84.geojson, selected_areas_alldata_areas_wgs84.geojson) su atrinktomis teritorijomis ir pridėtomis skaičiavimų reikšmėmis gali būti pasiekiami [aplanke geojson.](./geojson) Pagrindinės duomenų stulpelių reikšmės išlieka tokios pačios kaip nurodyta šiame skyriuje, o papildomų stulpelių reikšmės identiškos [skaičiavimo duomenų reikšmėms.](https://github.com/justas-it/kirtavieciu_ndvi_projektas#skai%C4%8Diavim%C5%B3-duomenys)

---

## Skaičiavimų duomenys

Skaičiavimų duomenys gali būti pasiekiami [aplanke csv.](./csv)

Bendras NDVI reikšmių paaiškinimas min: -1, max: 1, -1 žymi vandens buvimą, 0 žymi vegetacijos nebuvimą, 1 žymi vegetacijos buvimą.

Atributinės lentelės stulpelių reikšmės:

- no - mėnesio numeris.
- date_time - metai ir mėnesis.
- veg - Sentinel-2 SCL (4) vegetacijos reikšmė (min: 0, max: 1, 0 žymi vegetacijos nebuvimą, 1 žymi vegetacijos buvimą).
- so - Sentinel-2 SCL (5) žemės reikšmė (min: 0, max: 1, 0 žymi atviros žemės nebuvimą, 1 žymi atviros žemės buvimą).
- modis - MODIS Terra Daily NDVI reikšmės.
- s2_ndvi - Sentinel-2 ('B8', 'B4') NDVI reikšmės.
- fid - objekto kuriame buvo skaičiuojamos reikšmės fid numeris.
- gk - objekto kuriame buvo skaičiuojamos reikšmės geokodas.

Papildoma informacija:

- t-no_buffer...csv - teritorija be buferinės teritorijos.
- t-buffer...csv - tik teritorijos buferinė teritorija (150 m spinduliu apie teritorija).

---

## Python kodas

Python kodas naudotas duomenų skaičiavimams atlikti gali būti pasiekiamas [aplanke code.](./code)

**Svarbu! Pateiktas python kodas gali turėti klaidų ir veikti netinkamai.**

Apie detalesnį kodo veikimą galima suprasti peržiūrėjus kodo failą ir perskaičius komentarus (komentarai pateikti anglų kalba).

Supaprastinta kodo veikimo seka yra tokia:

1. Įkeliami duomenys iš GEE.
2. Sukuriamas datų sąrašas pagal nurodyta laiko intervalą.
3. Iš datų sąrašo paimamos datų poros apimančios mėnesio laikotarpį.
4. Mėnesio laikotarpiui apskaičiuojamas palydovinių duomenų reikšmių vidurkis nurodytoje teritorijoje.
5. Apskaičiuojamas nurodytos teritorijos pasirinkto mėnesio (visų teritorijos NDVI pikselių) reikšmių vidurkis.
6. Pasibaigus nurodytam laiko intervalui visi suskaičiuoti duomenys įrašomi į bilioteką.
7. Duomenys iš biliotekos išsaugomi .csv formatu, pavaizduojami grafike ir išsaugomi .png formatu, statistiškai dekomponuojami, pavaizduojami grafike ir išsaugomi .png formatu, nurodytos teritorijos ribos išsaugomos žemėlapyje .html formatu.

Papildoma informacija:

- Naudotos bibliotekos ir moduliai - ee, geemap, matplotlib, datetime, dateutil, pandas, numpy, statsmodels.
- Skirtumai tarp kodo failų - abu .ipynb failai yra beveik identiški, skiriasi tik kodo pradžia kur failo ee_ndvi_github_buffer.ipynb atveju kintamasis aoi yra ne kirtavietė, o buferis.

---

## Grafikų reikšmės

Ašių reikšmės:

- x - žymi laiko pokytį, pažymėta datomis su mėnesiais arba mėnesio skaičiumi intervale (2017-06-01 - 2022-08-01).
- y - žymi NDVI reikšmes arba Sentinel-2 SCL reikšmes.

Statistinės dekompozicijos reikšmės:

- Observed - duomenys be dekompozicijos.
- Trend - duomenų pokyčio tendencijos.
- Seasonal - sezoninis pokytis.
- Residual - statistinis triukšmas.

---

## Rezultatai

### Tyrimo objektas nr. 1

- fid = 111, kirtavietės aptikimo metai = 2020, sklypo geokodas = 14060349016.
- Teritorijos plotas - 28160 $m^2$.

Teritorija:
  
![111_img](./images/no_buffer_fid111.png)

![111_plot](./plots/t1-no_buffer111_plot.png)

![111_stats](./stats/t1-no_buffer111_stats.png)

Buferis:

![111_img](./images/buffer_fid111.png)

![111_plot](./plots/t1-buffer111_plot.png)

![111_stats](./stats/t1-buffer111_stats.png)

### Tyrimo objektas nr. 2

- fid = 203, kirtavietės aptikimo metai = 2019, sklypo geokodas = 60070094010.
- Teritorijos plotas - 29319 $m^2$.

Teritorija:

![111_img](./images/no_buffer_fid203.png)

![111_plot](./plots/t1-no_buffer203_plot.png)

![111_stats](./stats/t1-no_buffer203_stats.png)

Buferis:

![111_img](./images/buffer_fid203.png)

![111_plot](./plots/t1-buffer203_plot.png)

![111_stats](./stats/t1-buffer203_stats.png)

### Tyrimo objektas nr. 3

- fid = 35, kirtavietės aptikimo metai = 2019, sklypo geokodas = 19010163004.
- Teritorijos plotas - 25193 $m^2$.

Teritorija:

![111_img](./images/no_buffer_fid35.png)

![111_plot](./plots/t1-no_buffer35_plot.png)

![111_stats](./stats/t1-no_buffer35_stats.png)

Buferis:

![111_img](./images/buffer_fid35.png)

![111_plot](./plots/t1-no_buffer35_plot.png)

![111_stats](./stats/t1-no_buffer35_stats.png)

### Tyrimo objektas nr. 4

- fid = 15, kirtavietės aptikimo metai = 2019, sklypo geokodas = 22050085001.
- Teritorijos plotas - 23226 $m^2$.

Teritorija:

![111_img](./images/no_buffer_fid15.png)

![111_plot](./plots/t1-no_buffer15_plot.png)

![111_stats](./stats/t1-no_buffer15_stats.png)

Buferis:

![111_img](./images/buffer_fid15.png)

![111_plot](./plots/t1-buffer15_plot.png)

![111_stats](./stats/t1-buffer15_stats.png)

### Tyrimo objektas nr. 5

- fid = 192, kirtavietės aptikimo metai = 2019, sklypo geokodas = 59070487009.
- Teritorijos plotas - 41078 $m^2$.

Teritorija:

![111_img](./images/no_buffer_fid192.png)

![111_plot](./plots/t1-no_buffer192_plot.png)

![111_stats](./stats/t1-no_buffer192_stats.png)

Buferis:

![111_img](./images/buffer_fid192.png)

![111_plot](./plots/t1-buffer192_plot.png)

![111_stats](./stats/t1-buffer192_stats.png)

---

## Darbo rezultatų aptarimas

Darbo rezultatai rodo, kad identifikuoti kirtavietę tik iš NDVI skaitinių reikšmių gali būti sudėtinga. Kaip matoma palyginus kirtavietės teritorija ir buferine teritorija, NDVI reikšmės ne visada reikšmingai skiriasi. Žinoma, nereikia atmesti galimo varianto, kad suskaičiuotos NDVI reikšmės gali būti netikslios dėl naudotų skaičiavimo metodų, programavimo klaidų ar GEE ypatybių. Konkretesniems rezultatams gauti reikėtų tolesnių detalių tyrimų.

---

## Papildomas tyrimas

Duomenys susiję su papildomu tyrimu gali būti pasiekiami [aplanke ndvi_compare.](./ndvi_compare)

Buvo pamėginta palyginti NDVI reikšmes iš dviejų skirtingų šaltinių siekiant bent apytiksliai įsitikinti, kad GEE NDVI reikšmės atitinka realias NDVI reikšmes.

NDVI šaltiniai:

- Apskaičiuota pasitelkiant Sentinel-2 vaizdą atsisiųstą iš [Open Access Hub](https://scihub.copernicus.eu/) ir Qgis.
- Apskaičiuota pasitelkiant GEE Python API.

Darbo procesas:

1. Sukurti du NDVI reikšmes turintys rastrai.
2. Sukurtas taškų tinklelis pagal rastrų geografinę aprėptį (taškų tinklelis reikalingas tyrimo supaprastinimui, nes rastrų pikseliai turi skirtingas rezoliucijas).
3. Taškų tinklelio taškams priskirtos po jais esančių rastro pikselių reikšmės.
4. Taškų tinkeliai su duomenimis išsaugoti .csv formatu.
5. Duomenys pavaizduoti grafikuose.

### Qgis rezultatas

- Apytikslės rastro reikšmės - min: 0.1, max: 0.3.

<img src="./ndvi_compare/tif/qgis_rendered_img.png" width="50%" height="50%">

### GEE rezultatas

- Apytikslės rastro reikšmės - min: 0.1 , max: 0.4.

<img src="./ndvi_compare/tif/ee_rendered_img.png" width="50%" height="50%">

### Grafikas

![ee_qgis_plot](./ndvi_compare/plots/ee_qgis_plot.png)

### Papildomo tyrimo rezultatų aptarimas

Papildomas tyrimas nėra visiškai tikslus, bet parodo, kad GEE NDVI reikšmės (tikėtina) turi truputį didesnį teigiamą poslinkį nei Qgis NDVI reikšmės. Taip galimai yra dėl to, kad iš GEE buvo eksportuotas rastras turintis mažesnę erdvinę raišką nei Qgis rastras. Taip pat galimi skirtumai dėl programų skaičiavimo metodų - GEE kode skaičiuojant nebuvo nurodyta erdvinė rezoliucija (buvo nurodyta tik eksportuojant rastrą) dėl to nėra visiškai aišku kokią erdvinę rezoliuciją GEE naudojo skaičiavimams (tikėtina 10 m). Konkretesniems rezultatams gauti reikėtų tolesnių detalių tyrimų.
