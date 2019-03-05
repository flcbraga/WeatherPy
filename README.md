
# Observed Trends
1) The max temperature are near the equator. When the latitude increases (towards North pole) or decreases (toward South pole), the max temperature drops. 

2) Humidity and cloudiness do not seem to be correlated to latitude as there is a relatively uniform spread across the different latitude points. 

3) The majority of cities have a wind speed under 20 mph.  


```python
#Dependencies
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import requests
import time
import random
!pip install citipy
from citipy import citipy
from config import api_key
#set style for plots
plt.style.use('seaborn-talk')

```

    Requirement already satisfied: citipy in c:\programdata\anaconda3\lib\site-packages (0.0.5)
    Requirement already satisfied: kdtree>=0.12 in c:\programdata\anaconda3\lib\site-packages (from citipy) (0.16)


```python
#Creating list of lats/lngs
lats = range(-90, 90)
lngs = range(-180, 180)
city_list = []
for lat in lats:
    for lng in lngs:
        city = citipy.nearest_city(lat, lng)
        city_name = city.city_name
        city_list.append(city_name)

# Do some cleaning (remove duplicates)
city_df = pd.DataFrame(city_list)
new_city_df = city_df.drop_duplicates()
```


```python
#Extracting sample cities
city_samples = new_city_df.sample(750)
city_samples = city_samples.reset_index(drop = True)
city_samples.columns = ["City"]
city_samples.index += 1 
city_samples["Lat"] = ""
city_samples["Lng"] = ""
city_samples["Country"] = ""
city_samples["Date"] = ""
city_samples["Max Temp(°F) "] = ""
city_samples["Humidity(%)"] = ""
city_samples["Cloudiness(%)"] = ""
city_samples["Wind Speed(mph)"] = ""
city_samples.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Country</th>
      <th>Date</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>smithers</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>tirat karmel</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>igrim</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>santa rosa</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>5</th>
      <td>cavalcante</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



# OpenWeatherMap API Weather Check


```python
units = "Imperial"
print("Beginning Data Retrieval")
print("-------------------------------")
for index,row in city_samples.iterrows():
    city_name = row["City"]
    city_url_name = city_name.replace(" ", "%20")
    target_url = "http://api.openweathermap.org/data/2.5/weather?units=%s&APPID=%s&q=%s" % (units, api_key, city_url_name)
    city_weather = requests.get(target_url).json()
    city_samples.set_value(index,"Lat",city_weather.get("coord",{}).get("lat"))
    city_samples.set_value(index,"Lng",city_weather.get("coord",{}).get("lon"))
    city_samples.set_value(index,"Country",city_weather.get("sys",{}).get("country"))
    city_samples.set_value(index,"Date",city_weather.get("dt",{}))
    city_samples.set_value(index,"Max Temp(°F)",city_weather.get("main",{}).get("temp_max"))
    city_samples.set_value(index,"Humidity(%)",city_weather.get("main",{}).get("humidity"))
    city_samples.set_value(index,"Cloudiness(%)",city_weather.get("clouds",{}).get("all"))
    city_samples.set_value(index,"Wind Speed(mph)",city_weather.get("wind",{}).get("speed"))
    print("Processing City " + str(index) + " of 750 - " + str.title(city_name))
    print_url = "http://api.openweathermap.org/data/2.5/weather?units=%s&APPID=%s&q=%s" % (units, "api_key", city_url_name)
    print(print_url)
print("-------------------------------")
print("Data Retrieval Complete")
print("-------------------------------")


```

    Beginning Data Retrieval
-------------------------------
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:9: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  if __name__ == '__main__':
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:10: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  Remove the CWD from sys.path while we load stuff.
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:11: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  This is added back by InteractiveShellApp.init_path()
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:12: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  if sys.path[0] == '':
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:13: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  del sys.path[0]
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:14: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  from ipykernel import kernelapp as app
C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:16: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  app.launch_new_instance()
    Processing City 1 of 750 - Smithers
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=smithers
    Processing City 2 of 750 - Tirat Karmel
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tirat%20karmel
                Processing City 3 of 750 - Igrim
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=igrim
                Processing City 4 of 750 - Santa Rosa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=santa%20rosa
                Processing City 5 of 750 - Cavalcante
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cavalcante
                Processing City 6 of 750 - Lazo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lazo
                Processing City 7 of 750 - Chodziez
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chodziez
                Processing City 8 of 750 - Zlobin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zlobin
                Processing City 9 of 750 - Zhongshu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zhongshu
                Processing City 10 of 750 - Serra
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=serra
                Processing City 11 of 750 - Vygonichi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vygonichi
                Processing City 12 of 750 - Lagos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lagos
                Processing City 13 of 750 - Powell River
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=powell%20river
                Processing City 14 of 750 - Safwah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=safwah
                Processing City 15 of 750 - Midland
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=midland
                Processing City 16 of 750 - Brasileia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=brasileia
                Processing City 17 of 750 - Badvel
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=badvel
                Processing City 18 of 750 - Anna Paulowna
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=anna%20paulowna
                Processing City 19 of 750 - Pa Sang
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pa%20sang
                Processing City 20 of 750 - Nosy Varika
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nosy%20varika
                Processing City 21 of 750 - Kysyl-Syr
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kysyl-syr
                Processing City 22 of 750 - Quzhou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=quzhou
                Processing City 23 of 750 - Corpus Christi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=corpus%20christi
                Processing City 24 of 750 - Bella Union
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bella%20union
                Processing City 25 of 750 - Pirgos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pirgos
                Processing City 26 of 750 - Maningrida
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=maningrida
                Processing City 27 of 750 - Jucurutu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jucurutu
                Processing City 28 of 750 - Rach Gia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rach%20gia
                Processing City 29 of 750 - Tarnow
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tarnow
                Processing City 30 of 750 - Los Alamos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=los%20alamos
                Processing City 31 of 750 - Grimshaw
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=grimshaw
                Processing City 32 of 750 - Ifakara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ifakara
                Processing City 33 of 750 - Nynashamn
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nynashamn
                Processing City 34 of 750 - Sao Geraldo Do Araguaia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sao%20geraldo%20do%20araguaia
                Processing City 35 of 750 - Rio Grande
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rio%20grande
                Processing City 36 of 750 - Mudbidri
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mudbidri
                Processing City 37 of 750 - Bondoukou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bondoukou
                Processing City 38 of 750 - Windsor
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=windsor
                Processing City 39 of 750 - Rajo Khanani
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rajo%20khanani
                Processing City 40 of 750 - Cienaga
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cienaga
                Processing City 41 of 750 - Neuruppin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=neuruppin
                Processing City 42 of 750 - Smolensk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=smolensk
                Processing City 43 of 750 - Mega
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mega
                Processing City 44 of 750 - Plouzane
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=plouzane
                Processing City 45 of 750 - Dawei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dawei
                Processing City 46 of 750 - Kamiiso
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kamiiso
                Processing City 47 of 750 - Gordeyevka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gordeyevka
                Processing City 48 of 750 - Darlawn
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=darlawn
                Processing City 49 of 750 - Khagrachari
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=khagrachari
                Processing City 50 of 750 - Kankaanpaa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kankaanpaa
                Processing City 51 of 750 - Stavropol
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=stavropol
                Processing City 52 of 750 - Newport
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=newport
                Processing City 53 of 750 - Lunenburg
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lunenburg
                Processing City 54 of 750 - North Myrtle Beach
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=north%20myrtle%20beach
                Processing City 55 of 750 - Wencheng
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wencheng
                Processing City 56 of 750 - Sokoni
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sokoni
                Processing City 57 of 750 - Yuzhno-Kurilsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yuzhno-kurilsk
                Processing City 58 of 750 - Muyezerskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=muyezerskiy
                Processing City 59 of 750 - Pimentel
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pimentel
                Processing City 60 of 750 - Aviles
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=aviles
                Processing City 61 of 750 - Moundsville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=moundsville
                Processing City 62 of 750 - Cervo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cervo
                Processing City 63 of 750 - Cabras
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cabras
                Processing City 64 of 750 - Seydi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=seydi
                Processing City 65 of 750 - Suileng
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=suileng
                Processing City 66 of 750 - Garden Acres
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=garden%20acres
                Processing City 67 of 750 - Tomelloso
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tomelloso
                Processing City 68 of 750 - Nigde
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nigde
                Processing City 69 of 750 - Kaili
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kaili
                Processing City 70 of 750 - Mitu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mitu
                Processing City 71 of 750 - Suwalki
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=suwalki
                Processing City 72 of 750 - Makkaveyevo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=makkaveyevo
                Processing City 73 of 750 - Ahar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ahar
                Processing City 74 of 750 - Nakhon Phanom
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nakhon%20phanom
                Processing City 75 of 750 - Bow Island
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bow%20island
                Processing City 76 of 750 - Simbahan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=simbahan
                Processing City 77 of 750 - El Dorado
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=el%20dorado
                Processing City 78 of 750 - Kinanah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kinanah
                Processing City 79 of 750 - Saint-Jean-De-Braye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=saint-jean-de-braye
                Processing City 80 of 750 - Senj
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=senj
                Processing City 81 of 750 - Gombong
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gombong
                Processing City 82 of 750 - Bitam
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bitam
                Processing City 83 of 750 - Mpika
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mpika
                Processing City 84 of 750 - Las Cruces
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=las%20cruces
                Processing City 85 of 750 - Ganganagar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ganganagar
                Processing City 86 of 750 - Duminichi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=duminichi
                Processing City 87 of 750 - Snasa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=snasa
                Processing City 88 of 750 - Uray
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=uray
                Processing City 89 of 750 - Rutigliano
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rutigliano
                Processing City 90 of 750 - Hazleton
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hazleton
                Processing City 91 of 750 - Lewistown
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lewistown
                Processing City 92 of 750 - Massaguet
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=massaguet
                Processing City 93 of 750 - Danilov
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=danilov
                Processing City 94 of 750 - Geresk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=geresk
                Processing City 95 of 750 - Alpena
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=alpena
                Processing City 96 of 750 - Quebo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=quebo
                Processing City 97 of 750 - Mergui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mergui
                Processing City 98 of 750 - Kabalo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kabalo
                Processing City 99 of 750 - Marau
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=marau
                Processing City 100 of 750 - Uarini
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=uarini
                Processing City 101 of 750 - Cerinza
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cerinza
                Processing City 102 of 750 - Petropavlovsk-Kamchatskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=petropavlovsk-kamchatskiy
                Processing City 103 of 750 - Kyaikto
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kyaikto
                Processing City 104 of 750 - Vila Do Maio
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vila%20do%20maio
                Processing City 105 of 750 - Mankono
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mankono
                Processing City 106 of 750 - Barrow
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=barrow
                Processing City 107 of 750 - Bubaque
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bubaque
                Processing City 108 of 750 - Zhanakorgan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zhanakorgan
                Processing City 109 of 750 - Buriti Alegre
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=buriti%20alegre
                Processing City 110 of 750 - Ximei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ximei
                Processing City 111 of 750 - Werneck
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=werneck
                Processing City 112 of 750 - Arkhara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=arkhara
                Processing City 113 of 750 - Nemuro
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nemuro
                Processing City 114 of 750 - Butler
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=butler
                Processing City 115 of 750 - Primo Tapia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=primo%20tapia
                Processing City 116 of 750 - Kidal
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kidal
                Processing City 117 of 750 - Bang Saphan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bang%20saphan
                Processing City 118 of 750 - Arona
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=arona
                Processing City 119 of 750 - Nawa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nawa
                Processing City 120 of 750 - Huangmei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=huangmei
                Processing City 121 of 750 - Lobito
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lobito
                Processing City 122 of 750 - Mairana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mairana
                Processing City 123 of 750 - Kahone
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kahone
                Processing City 124 of 750 - Tunduru
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tunduru
                Processing City 125 of 750 - Guiyang
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=guiyang
                Processing City 126 of 750 - Willmar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=willmar
                Processing City 127 of 750 - Truth Or Consequences
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=truth%20or%20consequences
                Processing City 128 of 750 - Zanesville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zanesville
                Processing City 129 of 750 - Sesheke
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sesheke
                Processing City 130 of 750 - Vikulovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vikulovo
                Processing City 131 of 750 - Santa Fe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=santa%20fe
                Processing City 132 of 750 - Shevchenkove
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=shevchenkove
                Processing City 133 of 750 - Shetpe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=shetpe
                Processing City 134 of 750 - Ust-Kalmanka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ust-kalmanka
                Processing City 135 of 750 - Phalombe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=phalombe
                Processing City 136 of 750 - Orange
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=orange
                Processing City 137 of 750 - Canmore
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=canmore
                Processing City 138 of 750 - Frederiksvaerk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=frederiksvaerk
                Processing City 139 of 750 - Gillette
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gillette
                Processing City 140 of 750 - Kem
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kem
                Processing City 141 of 750 - Gemena
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gemena
                Processing City 142 of 750 - Rudnogorsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rudnogorsk
                Processing City 143 of 750 - That Phanom
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=that%20phanom
                Processing City 144 of 750 - Delvine
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=delvine
                Processing City 145 of 750 - Polovinnoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=polovinnoye
                Processing City 146 of 750 - Manosque
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=manosque
                Processing City 147 of 750 - Chinique
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chinique
                Processing City 148 of 750 - Qandala
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=qandala
                Processing City 149 of 750 - Hansi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hansi
                Processing City 150 of 750 - Caramoran
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caramoran
                Processing City 151 of 750 - Eyl
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=eyl
                Processing City 152 of 750 - Malm
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=malm
                Processing City 153 of 750 - Manaia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=manaia
                Processing City 154 of 750 - Montrose
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=montrose
                Processing City 155 of 750 - Swan River
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=swan%20river
                Processing City 156 of 750 - Poyarkovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=poyarkovo
                Processing City 157 of 750 - Angermunde
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=angermunde
                Processing City 158 of 750 - Hit
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hit
                Processing City 159 of 750 - Muslyumovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=muslyumovo
                Processing City 160 of 750 - Ishlei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ishlei
                Processing City 161 of 750 - Salekhard
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=salekhard
                Processing City 162 of 750 - Kuandian
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kuandian
                Processing City 163 of 750 - Vendome
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vendome
                Processing City 164 of 750 - Choucheng
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=choucheng
                Processing City 165 of 750 - New Ulm
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=new%20ulm
                Processing City 166 of 750 - Haripur
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=haripur
                Processing City 167 of 750 - Mantua
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mantua
                Processing City 168 of 750 - Aleksandrov Gay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=aleksandrov%20gay
                Processing City 169 of 750 - Serebryanyy Bor
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=serebryanyy%20bor
                Processing City 170 of 750 - Emerald
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=emerald
                Processing City 171 of 750 - Morris
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=morris
                Processing City 172 of 750 - Awjilah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=awjilah
                Processing City 173 of 750 - Epernay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=epernay
                Processing City 174 of 750 - Etla
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=etla
                Processing City 175 of 750 - Capao Bonito
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=capao%20bonito
                Processing City 176 of 750 - Pervomayskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pervomayskiy
                Processing City 177 of 750 - Steinbach
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=steinbach
                Processing City 178 of 750 - Itapora
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=itapora
                Processing City 179 of 750 - Sinjar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sinjar
                Processing City 180 of 750 - Kumano
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kumano
                Processing City 181 of 750 - Belyy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=belyy
                Processing City 182 of 750 - Wahpeton
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wahpeton
                Processing City 183 of 750 - Kaitangata
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kaitangata
                Processing City 184 of 750 - Ojhar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ojhar
                Processing City 185 of 750 - Gbadolite
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gbadolite
                Processing City 186 of 750 - Adre
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=adre
                Processing City 187 of 750 - Peniche
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=peniche
                Processing City 188 of 750 - Beloha
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=beloha
                Processing City 189 of 750 - Stryn
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=stryn
                Processing City 190 of 750 - Saint-Raphael
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=saint-raphael
                Processing City 191 of 750 - Sakti
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sakti
                Processing City 192 of 750 - Mason City
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mason%20city
                Processing City 193 of 750 - Hecelchakan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hecelchakan
                Processing City 194 of 750 - Zelenogorskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zelenogorskiy
                Processing City 195 of 750 - Ichhawar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ichhawar
                Processing City 196 of 750 - Kirensk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kirensk
                Processing City 197 of 750 - Kinsale
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kinsale
                Processing City 198 of 750 - Miram Shah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=miram%20shah
                Processing City 199 of 750 - Bustonkala
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bustonkala
                Processing City 200 of 750 - La Peca
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20peca
                Processing City 201 of 750 - Diofior
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=diofior
                Processing City 202 of 750 - Nyamuswa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nyamuswa
                Processing City 203 of 750 - Huron
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=huron
                Processing City 204 of 750 - Manono
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=manono
                Processing City 205 of 750 - Rumoi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rumoi
                Processing City 206 of 750 - Argentan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=argentan
                Processing City 207 of 750 - Linchuan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=linchuan
                Processing City 208 of 750 - Tzucacab
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tzucacab
                Processing City 209 of 750 - Hondo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hondo
                Processing City 210 of 750 - Lowestoft
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lowestoft
                Processing City 211 of 750 - Ruswil
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ruswil
                Processing City 212 of 750 - Holice
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=holice
                Processing City 213 of 750 - Kez
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kez
                Processing City 214 of 750 - Sangin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sangin
                Processing City 215 of 750 - Colonial Heights
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=colonial%20heights
                Processing City 216 of 750 - Zhengjiatun
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zhengjiatun
                Processing City 217 of 750 - Qujing
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=qujing
                Processing City 218 of 750 - Mahajanga
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mahajanga
                Processing City 219 of 750 - Mudon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mudon
                Processing City 220 of 750 - Kargasok
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kargasok
                Processing City 221 of 750 - Urubicha
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=urubicha
                Processing City 222 of 750 - Kazachinskoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kazachinskoye
                Processing City 223 of 750 - Melita
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=melita
                Processing City 224 of 750 - Arkadelphia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=arkadelphia
                Processing City 225 of 750 - Bordighera
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bordighera
                Processing City 226 of 750 - San Narciso
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20narciso
                Processing City 227 of 750 - Gagnoa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gagnoa
                Processing City 228 of 750 - Olbia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=olbia
                Processing City 229 of 750 - Posse
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=posse
                Processing City 230 of 750 - Sandpoint
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sandpoint
                Processing City 231 of 750 - Vanimo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vanimo
                Processing City 232 of 750 - Santa Teresa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=santa%20teresa
                Processing City 233 of 750 - Xuanhua
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=xuanhua
                Processing City 234 of 750 - Point Pleasant
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=point%20pleasant
                Processing City 235 of 750 - Fushe-Arrez
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=fushe-arrez
                Processing City 236 of 750 - Nanortalik
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nanortalik
                Processing City 237 of 750 - Batken
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=batken
                Processing City 238 of 750 - Micheweni
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=micheweni
                Processing City 239 of 750 - Les Escoumins
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=les%20escoumins
                Processing City 240 of 750 - Masindi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=masindi
                Processing City 241 of 750 - Badulla
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=badulla
                Processing City 242 of 750 - Carbonear
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=carbonear
                Processing City 243 of 750 - Werda
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=werda
                Processing City 244 of 750 - Barra
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=barra
                Processing City 245 of 750 - Almeirim
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=almeirim
                Processing City 246 of 750 - Caramay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caramay
                Processing City 247 of 750 - Thurso
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=thurso
                Processing City 248 of 750 - Salina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=salina
                Processing City 249 of 750 - San Angelo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20angelo
                Processing City 250 of 750 - Pokrovsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pokrovsk
                Processing City 251 of 750 - Synya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=synya
                Processing City 252 of 750 - Kosjeric
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kosjeric
                Processing City 253 of 750 - Sinait
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sinait
                Processing City 254 of 750 - Manica
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=manica
                Processing City 255 of 750 - Nkongsamba
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nkongsamba
                Processing City 256 of 750 - Sveti Nikole
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sveti%20nikole
                Processing City 257 of 750 - Stulovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=stulovo
                Processing City 258 of 750 - Dzaoudzi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dzaoudzi
                Processing City 259 of 750 - Healdsburg
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=healdsburg
                Processing City 260 of 750 - Gualeguay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gualeguay
                Processing City 261 of 750 - Wuwei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wuwei
                Processing City 262 of 750 - Plettenberg Bay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=plettenberg%20bay
                Processing City 263 of 750 - Mutoko
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mutoko
                Processing City 264 of 750 - Dharchula
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dharchula
                Processing City 265 of 750 - Busayra
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=busayra
                Processing City 266 of 750 - Coatesville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=coatesville
                Processing City 267 of 750 - Bang Lamung
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bang%20lamung
                Processing City 268 of 750 - Jaramana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jaramana
                Processing City 269 of 750 - Guelengdeng
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=guelengdeng
                Processing City 270 of 750 - Bakhmach
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bakhmach
                Processing City 271 of 750 - Lebanon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lebanon
                Processing City 272 of 750 - Petropavlovka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=petropavlovka
                Processing City 273 of 750 - Ouidah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ouidah
                Processing City 274 of 750 - Chirongui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chirongui
                Processing City 275 of 750 - Birin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=birin
                Processing City 276 of 750 - Las Varas
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=las%20varas
                Processing City 277 of 750 - Adrar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=adrar
                Processing City 278 of 750 - Capitan Bado
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=capitan%20bado
                Processing City 279 of 750 - Mukhen
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mukhen
                Processing City 280 of 750 - Bundaberg
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bundaberg
                Processing City 281 of 750 - Osypenko
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=osypenko
                Processing City 282 of 750 - Deputatskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=deputatskiy
                Processing City 283 of 750 - Tapejara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tapejara
                Processing City 284 of 750 - Lingao
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lingao
                Processing City 285 of 750 - Tiebissou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tiebissou
                Processing City 286 of 750 - Malacacheta
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=malacacheta
                Processing City 287 of 750 - Hebi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hebi
                Processing City 288 of 750 - Tarko-Sale
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tarko-sale
                Processing City 289 of 750 - Minsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=minsk
                Processing City 290 of 750 - Kota Belud
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kota%20belud
                Processing City 291 of 750 - Beyneu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=beyneu
                Processing City 292 of 750 - Yulin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yulin
                Processing City 293 of 750 - Wangou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wangou
                Processing City 294 of 750 - Sudak
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sudak
                Processing City 295 of 750 - Boyuibe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=boyuibe
                Processing City 296 of 750 - Rajampet
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rajampet
                Processing City 297 of 750 - Abomey
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=abomey
                Processing City 298 of 750 - Kashary
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kashary
                Processing City 299 of 750 - Nybro
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nybro
                Processing City 300 of 750 - Raipur
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=raipur
                Processing City 301 of 750 - Mahenge
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mahenge
                Processing City 302 of 750 - Pirenopolis
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pirenopolis
                Processing City 303 of 750 - Debre Tabor
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=debre%20tabor
                Processing City 304 of 750 - Bobrov
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bobrov
                Processing City 305 of 750 - Gopalpur
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gopalpur
                Processing City 306 of 750 - Kutahya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kutahya
                Processing City 307 of 750 - Canutama
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=canutama
                Processing City 308 of 750 - Talara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=talara
                Processing City 309 of 750 - Shizilu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=shizilu
                Processing City 310 of 750 - Jurm
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jurm
                Processing City 311 of 750 - Helong
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=helong
                Processing City 312 of 750 - Kungalv
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kungalv
                Processing City 313 of 750 - Ipatovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ipatovo
                Processing City 314 of 750 - Angol
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=angol
                Processing City 315 of 750 - Angren
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=angren
                Processing City 316 of 750 - Kaspiysk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kaspiysk
                Processing City 317 of 750 - Iisalmi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=iisalmi
                Processing City 318 of 750 - Cumana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cumana
                Processing City 319 of 750 - Azrow
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=azrow
                Processing City 320 of 750 - Maneadero
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=maneadero
                Processing City 321 of 750 - Miranorte
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=miranorte
                Processing City 322 of 750 - Novouzensk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=novouzensk
                Processing City 323 of 750 - Pondicherry
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pondicherry
                Processing City 324 of 750 - Cotui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cotui
                Processing City 325 of 750 - Benicarlo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=benicarlo
                Processing City 326 of 750 - Jieshou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jieshou
                Processing City 327 of 750 - Williston
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=williston
                Processing City 328 of 750 - Seligenstadt
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=seligenstadt
                Processing City 329 of 750 - Izhmorskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=izhmorskiy
                Processing City 330 of 750 - Holme
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=holme
                Processing City 331 of 750 - Liwale
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=liwale
                Processing City 332 of 750 - Boshnyakovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=boshnyakovo
                Processing City 333 of 750 - Snihurivka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=snihurivka
                Processing City 334 of 750 - Ambodifototra
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ambodifototra
                Processing City 335 of 750 - Toora-Khem
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=toora-khem
                Processing City 336 of 750 - Nalgonda
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nalgonda
                Processing City 337 of 750 - Touros
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=touros
                Processing City 338 of 750 - La Asuncion
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20asuncion
                Processing City 339 of 750 - Talavera De La Reina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=talavera%20de%20la%20reina
                Processing City 340 of 750 - Tsaratanana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tsaratanana
                Processing City 341 of 750 - Gympie
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gympie
                Processing City 342 of 750 - Mehndawal
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mehndawal
                Processing City 343 of 750 - Leshukonskoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=leshukonskoye
                Processing City 344 of 750 - Amga
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=amga
                Processing City 345 of 750 - Addi Ugri
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=addi%20ugri
                Processing City 346 of 750 - Zaraza
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zaraza
                Processing City 347 of 750 - Diu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=diu
                Processing City 348 of 750 - Bunbury
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bunbury
                Processing City 349 of 750 - Chepo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chepo
                Processing City 350 of 750 - Petrozavodsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=petrozavodsk
                Processing City 351 of 750 - Ibiapina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ibiapina
                Processing City 352 of 750 - Numan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=numan
                Processing City 353 of 750 - Lesnoy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lesnoy
                Processing City 354 of 750 - Lang Suan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lang%20suan
                Processing City 355 of 750 - Kankan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kankan
                Processing City 356 of 750 - Soe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=soe
                Processing City 357 of 750 - Viking
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=viking
                Processing City 358 of 750 - Orje
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=orje
                Processing City 359 of 750 - Barao De Melgaco
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=barao%20de%20melgaco
                Processing City 360 of 750 - Ibate
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ibate
                Processing City 361 of 750 - Gistrup
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gistrup
                Processing City 362 of 750 - Cehegin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cehegin
                Processing City 363 of 750 - Lewisville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lewisville
                Processing City 364 of 750 - Karamken
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=karamken
                Processing City 365 of 750 - Mineral Wells
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mineral%20wells
                Processing City 366 of 750 - Kuvshinovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kuvshinovo
                Processing City 367 of 750 - Zasechnoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zasechnoye
                Processing City 368 of 750 - The Pas
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=the%20pas
                Processing City 369 of 750 - Aswan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=aswan
                Processing City 370 of 750 - Tarakan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tarakan
                Processing City 371 of 750 - La Baneza
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20baneza
                Processing City 372 of 750 - Tha Mai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tha%20mai
                Processing City 373 of 750 - Sao Raimundo Nonato
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sao%20raimundo%20nonato
                Processing City 374 of 750 - Rockport
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rockport
                Processing City 375 of 750 - Tamulte
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tamulte
                Processing City 376 of 750 - Bela Vista
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bela%20vista
                Processing City 377 of 750 - South Sioux City
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=south%20sioux%20city
                Processing City 378 of 750 - Brcko
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=brcko
                Processing City 379 of 750 - Norsup
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=norsup
                Processing City 380 of 750 - Can Tho
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=can%20tho
                Processing City 381 of 750 - Kilis
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kilis
                Processing City 382 of 750 - Boguchany
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=boguchany
                Processing City 383 of 750 - Najran
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=najran
                Processing City 384 of 750 - Aykhal
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=aykhal
                Processing City 385 of 750 - Zalaszentgrot
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zalaszentgrot
                Processing City 386 of 750 - Srandakan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=srandakan
                Processing City 387 of 750 - Camacha
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=camacha
                Processing City 388 of 750 - Diosjeno
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=diosjeno
                Processing City 389 of 750 - Riyaq
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=riyaq
                Processing City 390 of 750 - Scottsboro
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=scottsboro
                Processing City 391 of 750 - Gayeri
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gayeri
                Processing City 392 of 750 - Montego Bay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=montego%20bay
                Processing City 393 of 750 - Athy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=athy
                Processing City 394 of 750 - Arkhipo-Osipovka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=arkhipo-osipovka
                Processing City 395 of 750 - Dunayivtsi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dunayivtsi
                Processing City 396 of 750 - Menzelinsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=menzelinsk
                Processing City 397 of 750 - Lenine
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lenine
                Processing City 398 of 750 - Higuey
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=higuey
                Processing City 399 of 750 - Inderborskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=inderborskiy
                Processing City 400 of 750 - Effingham
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=effingham
                Processing City 401 of 750 - Namanyere
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=namanyere
                Processing City 402 of 750 - Sovetskaya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sovetskaya
                Processing City 403 of 750 - Mormugao
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mormugao
                Processing City 404 of 750 - Amozoc
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=amozoc
                Processing City 405 of 750 - Nizhniy Bestyakh
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nizhniy%20bestyakh
                Processing City 406 of 750 - Otane
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=otane
                Processing City 407 of 750 - Kibaya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kibaya
                Processing City 408 of 750 - Excelsior Springs
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=excelsior%20springs
                Processing City 409 of 750 - Terenos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=terenos
                Processing City 410 of 750 - Zverinogolovskoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zverinogolovskoye
                Processing City 411 of 750 - Kilembe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kilembe
                Processing City 412 of 750 - Tevaitoa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tevaitoa
                Processing City 413 of 750 - Pleshanovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pleshanovo
                Processing City 414 of 750 - Owensboro
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=owensboro
                Processing City 415 of 750 - Itoman
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=itoman
                Processing City 416 of 750 - Gerash
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gerash
                Processing City 417 of 750 - Amarante Do Maranhao
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=amarante%20do%20maranhao
                Processing City 418 of 750 - Lukulu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lukulu
                Processing City 419 of 750 - Walla Walla
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=walla%20walla
                Processing City 420 of 750 - Hakkari
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hakkari
                Processing City 421 of 750 - Kariya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kariya
                Processing City 422 of 750 - Votkinsk
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=votkinsk
                Processing City 423 of 750 - Coldwater
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=coldwater
                Processing City 424 of 750 - Loutros
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=loutros
                Processing City 425 of 750 - Pyaozerskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pyaozerskiy
                Processing City 426 of 750 - Mporokoso
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mporokoso
                Processing City 427 of 750 - Chicama
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chicama
                Processing City 428 of 750 - Novochernorechenskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=novochernorechenskiy
                Processing City 429 of 750 - Devrek
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=devrek
                Processing City 430 of 750 - Upig
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=upig
                Processing City 431 of 750 - Batagay-Alyta
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=batagay-alyta
                Processing City 432 of 750 - Pousat
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pousat
                Processing City 433 of 750 - Chagda
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chagda
                Processing City 434 of 750 - Cheremkhovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cheremkhovo
                Processing City 435 of 750 - Logumkloster
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=logumkloster
                Processing City 436 of 750 - Rangoon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rangoon
                Processing City 437 of 750 - Gondanglegi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gondanglegi
                Processing City 438 of 750 - Winchester
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=winchester
                Processing City 439 of 750 - Xinzhi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=xinzhi
                Processing City 440 of 750 - Bom Jesus
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bom%20jesus
                Processing City 441 of 750 - Cam Pha
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=cam%20pha
                Processing City 442 of 750 - Tobane
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tobane
                Processing City 443 of 750 - Bayir
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bayir
                Processing City 444 of 750 - Tacuarembo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tacuarembo
                Processing City 445 of 750 - Hendaye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hendaye
                Processing City 446 of 750 - Casablanca
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=casablanca
                Processing City 447 of 750 - Nizhniy Tsasuchey
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nizhniy%20tsasuchey
                Processing City 448 of 750 - Pozo Colorado
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pozo%20colorado
                Processing City 449 of 750 - Suleja
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=suleja
                Processing City 450 of 750 - Gunnedah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gunnedah
                Processing City 451 of 750 - Znamenskoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=znamenskoye
                Processing City 452 of 750 - Longview
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=longview
                Processing City 453 of 750 - Caucasia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caucasia
                Processing City 454 of 750 - Burica
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=burica
                Processing City 455 of 750 - Gescher
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gescher
                Processing City 456 of 750 - Campo Verde
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=campo%20verde
                Processing City 457 of 750 - Hutchinson
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hutchinson
                Processing City 458 of 750 - Yhu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yhu
                Processing City 459 of 750 - Nalut
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nalut
                Processing City 460 of 750 - Alyangula
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=alyangula
                Processing City 461 of 750 - Jaru
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jaru
                Processing City 462 of 750 - Pilar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pilar
                Processing City 463 of 750 - Tengzhou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tengzhou
                Processing City 464 of 750 - San Mateo Del Mar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20mateo%20del%20mar
                Processing City 465 of 750 - Huangpi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=huangpi
                Processing City 466 of 750 - Belaya Glina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=belaya%20glina
                Processing City 467 of 750 - Plaridel
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=plaridel
                Processing City 468 of 750 - Caracuaro
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caracuaro
                Processing City 469 of 750 - Bukama
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bukama
                Processing City 470 of 750 - Glyadyanskoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=glyadyanskoye
                Processing City 471 of 750 - Alto Araguaia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=alto%20araguaia
                Processing City 472 of 750 - Subtanjalla
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=subtanjalla
                Processing City 473 of 750 - Brumunddal
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=brumunddal
                Processing City 474 of 750 - Superior
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=superior
                Processing City 475 of 750 - Corinto
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=corinto
                Processing City 476 of 750 - Omsukchan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=omsukchan
                Processing City 477 of 750 - Krabi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=krabi
                Processing City 478 of 750 - Opobo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=opobo
                Processing City 479 of 750 - Katsiveli
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=katsiveli
                Processing City 480 of 750 - Ayan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ayan
                Processing City 481 of 750 - Torrox
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=torrox
                Processing City 482 of 750 - Copacabana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=copacabana
                Processing City 483 of 750 - Grand Forks
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=grand%20forks
                Processing City 484 of 750 - Tranas
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tranas
                Processing City 485 of 750 - Wainwright
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wainwright
                Processing City 486 of 750 - Amod
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=amod
                Processing City 487 of 750 - Qazvin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=qazvin
                Processing City 488 of 750 - Rahon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rahon
                Processing City 489 of 750 - Yauya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yauya
                Processing City 490 of 750 - Rorvik
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rorvik
                Processing City 491 of 750 - La Ciotat
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20ciotat
                Processing City 492 of 750 - Abu Zabad
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=abu%20zabad
                Processing City 493 of 750 - Mandan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mandan
                Processing City 494 of 750 - Masvingo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=masvingo
                Processing City 495 of 750 - Kilkis
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kilkis
                Processing City 496 of 750 - Valkeala
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=valkeala
                Processing City 497 of 750 - Severo-Yeniseyskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=severo-yeniseyskiy
                Processing City 498 of 750 - Sandy Bay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sandy%20bay
                Processing City 499 of 750 - Pishin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pishin
                Processing City 500 of 750 - Fengrun
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=fengrun
                Processing City 501 of 750 - Hattiesburg
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hattiesburg
                Processing City 502 of 750 - Manicore
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=manicore
                Processing City 503 of 750 - Imabari
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=imabari
                Processing City 504 of 750 - Gamboula
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gamboula
                Processing City 505 of 750 - Dzhebariki-Khaya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dzhebariki-khaya
                Processing City 506 of 750 - Onguday
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=onguday
                Processing City 507 of 750 - Megion
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=megion
                Processing City 508 of 750 - Kupino
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kupino
                Processing City 509 of 750 - Boone
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=boone
                Processing City 510 of 750 - Bethanien
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bethanien
                Processing City 511 of 750 - Marawi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=marawi
                Processing City 512 of 750 - Laem Sing
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=laem%20sing
                Processing City 513 of 750 - Amalner
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=amalner
                Processing City 514 of 750 - Ayna
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ayna
                Processing City 515 of 750 - Nelson
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nelson
                Processing City 516 of 750 - George Town
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=george%20town
                Processing City 517 of 750 - Kokstad
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kokstad
                Processing City 518 of 750 - Chirkey
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chirkey
                Processing City 519 of 750 - Chik
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chik
                Processing City 520 of 750 - Ferkessedougou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ferkessedougou
                Processing City 521 of 750 - Vernon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vernon
                Processing City 522 of 750 - Minna
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=minna
                Processing City 523 of 750 - Wilmington Island
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wilmington%20island
                Processing City 524 of 750 - Imeni Poliny Osipenko
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=imeni%20poliny%20osipenko
                Processing City 525 of 750 - Rapid Valley
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rapid%20valley
                Processing City 526 of 750 - Whitehaven
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=whitehaven
                Processing City 527 of 750 - Wanganui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=wanganui
                Processing City 528 of 750 - Ketchikan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ketchikan
                Processing City 529 of 750 - Gwadar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gwadar
                Processing City 530 of 750 - San Patricio
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20patricio
                Processing City 531 of 750 - Paramirim
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=paramirim
                Processing City 532 of 750 - Asenovgrad
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=asenovgrad
                Processing City 533 of 750 - Biltine
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=biltine
                Processing City 534 of 750 - Tumen
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tumen
                Processing City 535 of 750 - Rodrigues Alves
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rodrigues%20alves
                Processing City 536 of 750 - Hwange
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hwange
                Processing City 537 of 750 - Tarancon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tarancon
                Processing City 538 of 750 - Santo Antonio Do Leverger
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=santo%20antonio%20do%20leverger
                Processing City 539 of 750 - San Ignacio
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20ignacio
                Processing City 540 of 750 - Presidente Venceslau
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=presidente%20venceslau
                Processing City 541 of 750 - Gobo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gobo
                Processing City 542 of 750 - Madison
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=madison
                Processing City 543 of 750 - Lyubech
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lyubech
                Processing City 544 of 750 - Woodward
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=woodward
                Processing City 545 of 750 - Garowe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=garowe
                Processing City 546 of 750 - Butte
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=butte
                Processing City 547 of 750 - Hanzhong
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hanzhong
                Processing City 548 of 750 - Sooke
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sooke
                Processing City 549 of 750 - Yerofey Pavlovich
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yerofey%20pavlovich
                Processing City 550 of 750 - Shirokiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=shirokiy
                Processing City 551 of 750 - Tamsweg
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tamsweg
                Processing City 552 of 750 - La Seyne-Sur-Mer
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20seyne-sur-mer
                Processing City 553 of 750 - Panama City
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=panama%20city
                Processing City 554 of 750 - Turgoyak
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=turgoyak
                Processing City 555 of 750 - Novobiryusinskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=novobiryusinskiy
                Processing City 556 of 750 - Dodola
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dodola
                Processing City 557 of 750 - Mitzic
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mitzic
                Processing City 558 of 750 - Idil
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=idil
                Processing City 559 of 750 - Bialogard
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bialogard
                Processing City 560 of 750 - Elk Point
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=elk%20point
                Processing City 561 of 750 - Erzurum
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=erzurum
                Processing City 562 of 750 - Mortka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mortka
                Processing City 563 of 750 - Nguruka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nguruka
                Processing City 564 of 750 - Sanghar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sanghar
                Processing City 565 of 750 - Jeremie
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jeremie
                Processing City 566 of 750 - Susehri
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=susehri
                Processing City 567 of 750 - Sakakah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sakakah
                Processing City 568 of 750 - Albacete
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=albacete
                Processing City 569 of 750 - Tabarqah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tabarqah
                Processing City 570 of 750 - Grandview
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=grandview
                Processing City 571 of 750 - Don Sak
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=don%20sak
                Processing City 572 of 750 - Upernavik
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=upernavik
                Processing City 573 of 750 - Kalmar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kalmar
                Processing City 574 of 750 - Curuca
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=curuca
                Processing City 575 of 750 - Jumla
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jumla
                Processing City 576 of 750 - Marfino
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=marfino
                Processing City 577 of 750 - Camacupa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=camacupa
                Processing City 578 of 750 - Mirnyy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mirnyy
                Processing City 579 of 750 - Panguna
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=panguna
                Processing City 580 of 750 - El Alto
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=el%20alto
                Processing City 581 of 750 - Kentau
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kentau
                Processing City 582 of 750 - Rancho Palos Verdes
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rancho%20palos%20verdes
                Processing City 583 of 750 - Tekeli
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tekeli
                Processing City 584 of 750 - Mpongwe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mpongwe
                Processing City 585 of 750 - Gorin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gorin
                Processing City 586 of 750 - Labytnangi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=labytnangi
                Processing City 587 of 750 - Camara De Lobos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=camara%20de%20lobos
                Processing City 588 of 750 - Kigoma
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kigoma
                Processing City 589 of 750 - Eufaula
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=eufaula
                Processing City 590 of 750 - Hollins
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hollins
                Processing City 591 of 750 - Abai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=abai
                Processing City 592 of 750 - Netrakona
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=netrakona
                Processing City 593 of 750 - Calafat
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=calafat
                Processing City 594 of 750 - Pankrushikha
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pankrushikha
                Processing City 595 of 750 - Susner
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=susner
                Processing City 596 of 750 - De-Kastri
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=de-kastri
                Processing City 597 of 750 - Schruns
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=schruns
                Processing City 598 of 750 - Anar Darreh
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=anar%20darreh
                Processing City 599 of 750 - Dong Hoi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dong%20hoi
                Processing City 600 of 750 - Soria
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=soria
                Processing City 601 of 750 - Viganello
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=viganello
                Processing City 602 of 750 - Zilupe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=zilupe
                Processing City 603 of 750 - Petauke
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=petauke
                Processing City 604 of 750 - Autun
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=autun
                Processing City 605 of 750 - Sawakin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sawakin
                Processing City 606 of 750 - Mehamn
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mehamn
                Processing City 607 of 750 - Paracuru
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=paracuru
                Processing City 608 of 750 - Bamora
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bamora
                Processing City 609 of 750 - Picota
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=picota
                Processing City 610 of 750 - Szalkszentmarton
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=szalkszentmarton
                Processing City 611 of 750 - Tashara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tashara
                Processing City 612 of 750 - Orocue
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=orocue
                Processing City 613 of 750 - Starosubkhangulovo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=starosubkhangulovo
                Processing City 614 of 750 - Arrifes
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=arrifes
                Processing City 615 of 750 - Challapata
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=challapata
                Processing City 616 of 750 - Richmond
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=richmond
                Processing City 617 of 750 - Xian
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=xian
                Processing City 618 of 750 - Pasighat
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pasighat
                Processing City 619 of 750 - Rawah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rawah
                Processing City 620 of 750 - Samfya
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=samfya
                Processing City 621 of 750 - Hargeysa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hargeysa
                Processing City 622 of 750 - Barhi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=barhi
                Processing City 623 of 750 - Bajos De Haina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bajos%20de%20haina
                Processing City 624 of 750 - Yerky
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yerky
                Processing City 625 of 750 - San Fernando
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=san%20fernando
                Processing City 626 of 750 - Trincomalee
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=trincomalee
                Processing City 627 of 750 - Handwara
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=handwara
                Processing City 628 of 750 - Sriperumbudur
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sriperumbudur
                Processing City 629 of 750 - Kalanguy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kalanguy
                Processing City 630 of 750 - Canon City
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=canon%20city
                Processing City 631 of 750 - Alenquer
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=alenquer
                Processing City 632 of 750 - Belmonte
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=belmonte
                Processing City 633 of 750 - Makurdi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=makurdi
                Processing City 634 of 750 - Labuhan
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=labuhan
                Processing City 635 of 750 - Loikaw
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=loikaw
                Processing City 636 of 750 - Tena
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tena
                Processing City 637 of 750 - Balimo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=balimo
                Processing City 638 of 750 - Changde
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=changde
                Processing City 639 of 750 - Tiznit
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tiznit
                Processing City 640 of 750 - Damietta
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=damietta
                Processing City 641 of 750 - Ambunti
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ambunti
                Processing City 642 of 750 - Sataua
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sataua
                Processing City 643 of 750 - Sayville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sayville
                Processing City 644 of 750 - Saint-Augustin
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=saint-augustin
                Processing City 645 of 750 - Lakhisarai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lakhisarai
                Processing City 646 of 750 - Putina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=putina
                Processing City 647 of 750 - Rupert
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=rupert
                Processing City 648 of 750 - Stillwater
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=stillwater
                Processing City 649 of 750 - Gueret
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gueret
                Processing City 650 of 750 - Lakes Entrance
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lakes%20entrance
                Processing City 651 of 750 - Oistins
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=oistins
                Processing City 652 of 750 - Kisanga
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kisanga
                Processing City 653 of 750 - La Plata
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20plata
                Processing City 654 of 750 - Denizli
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=denizli
                Processing City 655 of 750 - Castle Douglas
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=castle%20douglas
                Processing City 656 of 750 - Quixada
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=quixada
                Processing City 657 of 750 - Khuchni
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=khuchni
                Processing City 658 of 750 - Pyapon
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pyapon
                Processing City 659 of 750 - Camaragibe
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=camaragibe
                Processing City 660 of 750 - Caucaia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caucaia
                Processing City 661 of 750 - Santa Helena De Goias
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=santa%20helena%20de%20goias
                Processing City 662 of 750 - Daltenganj
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=daltenganj
                Processing City 663 of 750 - Mingguang
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mingguang
                Processing City 664 of 750 - Praia
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=praia
                Processing City 665 of 750 - Sukhoy Log
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sukhoy%20log
                Processing City 666 of 750 - Ocos
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ocos
                Processing City 667 of 750 - Asuncion
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=asuncion
                Processing City 668 of 750 - Mmabatho
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mmabatho
                Processing City 669 of 750 - Kaiu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kaiu
                Processing City 670 of 750 - Nueva Gerona
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nueva%20gerona
                Processing City 671 of 750 - Pont-A-Mousson
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pont-a-mousson
                Processing City 672 of 750 - Ouro Fino
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ouro%20fino
                Processing City 673 of 750 - Xinyang
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=xinyang
                Processing City 674 of 750 - Panshi
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=panshi
                Processing City 675 of 750 - Melito Di Porto Salvo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=melito%20di%20porto%20salvo
                Processing City 676 of 750 - Dianopolis
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dianopolis
                Processing City 677 of 750 - Nikel
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nikel
                Processing City 678 of 750 - Abay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=abay
                Processing City 679 of 750 - Twin Falls
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=twin%20falls
                Processing City 680 of 750 - Benjamin Constant
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=benjamin%20constant
                Processing City 681 of 750 - Uige
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=uige
                Processing City 682 of 750 - Valdez
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=valdez
                Processing City 683 of 750 - Kotido
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kotido
                Processing City 684 of 750 - Shush
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=shush
                Processing City 685 of 750 - Easton
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=easton
                Processing City 686 of 750 - Gusau
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gusau
                Processing City 687 of 750 - Severnoye
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=severnoye
                Processing City 688 of 750 - Carahue
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=carahue
                Processing City 689 of 750 - Armacao Dos Buzios
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=armacao%20dos%20buzios
                Processing City 690 of 750 - Ladwa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ladwa
                Processing City 691 of 750 - Meaux
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=meaux
                Processing City 692 of 750 - Jaleswar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=jaleswar
                Processing City 693 of 750 - Concord
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=concord
                Processing City 694 of 750 - Skibbereen
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=skibbereen
                Processing City 695 of 750 - Aksum
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=aksum
                Processing City 696 of 750 - Florida
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=florida
                Processing City 697 of 750 - Mambolo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mambolo
                Processing City 698 of 750 - Miracema Do Tocantins
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=miracema%20do%20tocantins
                Processing City 699 of 750 - Hojai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=hojai
                Processing City 700 of 750 - Nurota
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=nurota
                Processing City 701 of 750 - Conway
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=conway
                Processing City 702 of 750 - Francisco Beltrao
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=francisco%20beltrao
                Processing City 703 of 750 - El Cobre
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=el%20cobre
                Processing City 704 of 750 - La Ronge
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=la%20ronge
                Processing City 705 of 750 - Kamina
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=kamina
                Processing City 706 of 750 - Malkapur
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=malkapur
                Processing City 707 of 750 - Valdobbiadene
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=valdobbiadene
                Processing City 708 of 750 - Mar Del Plata
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mar%20del%20plata
                Processing City 709 of 750 - Vao
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=vao
                Processing City 710 of 750 - Mandalay
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=mandalay
                Processing City 711 of 750 - North Saint Paul
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=north%20saint%20paul
                Processing City 712 of 750 - Caohai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=caohai
                Processing City 713 of 750 - Statesville
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=statesville
                Processing City 714 of 750 - Ljungby
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ljungby
                Processing City 715 of 750 - Burriana
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=burriana
                Processing City 716 of 750 - Ibicui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ibicui
                Processing City 717 of 750 - Chalmette
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=chalmette
                Processing City 718 of 750 - Obeliai
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=obeliai
                Processing City 719 of 750 - Dujuma
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=dujuma
                Processing City 720 of 750 - Lorengau
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lorengau
                Processing City 721 of 750 - Taksimo
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=taksimo
                Processing City 722 of 750 - Karwar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=karwar
                Processing City 723 of 750 - Naftah
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=naftah
                Processing City 724 of 750 - Xinyu
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=xinyu
                Processing City 725 of 750 - Saint Pete Beach
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=saint%20pete%20beach
                Processing City 726 of 750 - Madarounfa
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=madarounfa
                Processing City 727 of 750 - Pascagoula
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=pascagoula
                Processing City 728 of 750 - Canto Do Buriti
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=canto%20do%20buriti
                Processing City 729 of 750 - Francistown
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=francistown
                Processing City 730 of 750 - Skegness
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=skegness
                Processing City 731 of 750 - Gubkinskiy
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=gubkinskiy
                Processing City 732 of 750 - Teno
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=teno
                Processing City 733 of 750 - Olonets
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=olonets
                Processing City 734 of 750 - Suarez
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=suarez
                Processing City 735 of 750 - Narasannapeta
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=narasannapeta
                Processing City 736 of 750 - Khuldabad
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=khuldabad
                Processing City 737 of 750 - Bogande
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bogande
                Processing City 738 of 750 - General Pico
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=general%20pico
                Processing City 739 of 750 - Road Town
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=road%20town
                Processing City 740 of 750 - Guhagar
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=guhagar
                Processing City 741 of 750 - Fougamou
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=fougamou
                Processing City 742 of 750 - Ourossogui
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=ourossogui
                Processing City 743 of 750 - Marica
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=marica
                Processing City 744 of 750 - Yanam
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=yanam
                Processing City 745 of 750 - Da Lat
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=da%20lat
                Processing City 746 of 750 - Szentlorinc
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=szentlorinc
                Processing City 747 of 750 - Lidkoping
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=lidkoping
                Processing City 748 of 750 - Bolshaya Chernigovka
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=bolshaya%20chernigovka
                Processing City 749 of 750 - Tiarei
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=tiarei
                Processing City 750 of 750 - Sun Valley
                http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=api_key&q=sun%20valley
                -------------------------------
                Data Retrieval Complete
                -------------------------------

```
```python
#Clean up & save as CSV file
city_samples = city_samples.dropna()
city_samples.to_csv("weatherpy_data_results.csv")
city_samples.count(axis = 0)
```




    City               701
    Lat                701
    Lng                701
    Country            701
    Date               701
    Max Temp(°F)       701
    Humidity(%)        701
    Cloudiness(%)      701
    Wind Speed(mph)    701
    Max Temp(°F)       701
    dtype: int64



```python
city_samples.head()

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Country</th>
      <th>Date</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>seredka</td>
      <td>58.16</td>
      <td>28.19</td>
      <td>RU</td>
      <td>1513834200</td>
      <td>28.4</td>
      <td>92</td>
      <td>90</td>
      <td>11.18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>nikolayevsk-na-amure</td>
      <td>53.14</td>
      <td>140.73</td>
      <td>RU</td>
      <td>1513836148</td>
      <td>2.63</td>
      <td>76</td>
      <td>32</td>
      <td>9.33</td>
    </tr>
    <tr>
      <th>3</th>
      <td>qingdao</td>
      <td>36.1</td>
      <td>120.37</td>
      <td>CN</td>
      <td>1513832400</td>
      <td>48.2</td>
      <td>24</td>
      <td>0</td>
      <td>6.71</td>
    </tr>
    <tr>
      <th>4</th>
      <td>kumukh</td>
      <td>42.17</td>
      <td>47.12</td>
      <td>RU</td>
      <td>1513836148</td>
      <td>24.86</td>
      <td>74</td>
      <td>8</td>
      <td>1.16</td>
    </tr>
    <tr>
      <th>5</th>
      <td>were ilu</td>
      <td>10.6</td>
      <td>39.43</td>
      <td>ET</td>
      <td>1513836149</td>
      <td>48.84</td>
      <td>42</td>
      <td>0</td>
      <td>1.95</td>
    </tr>
  </tbody>
</table>
</div>

<div>
       <style scoped>
           .dataframe tbody tr th:only-of-type {
               vertical-align: middle;
           }
       
           .dataframe tbody tr th {
               vertical-align: top;
           }
       
           .dataframe thead th {
               text-align: right;
           }
       </style>
       <table border=\"1\" class=\"dataframe\">
         <thead>
           <tr style=\"text-align: right;\">
             <th></th>
             <th>City</th>
             <th>Lat</th>
             <th>Lng</th>
             <th>Country</th>
             <th>Date</th>
             <th>Max Temp(°F)</th>
             <th>Humidity(%)</th>
             <th>Cloudiness(%)</th>
             <th>Wind Speed(mph)</th>
             <th>Max Temp(°F)</th>
           </tr>
         </thead>
         <tbody>
           <tr>
             <th>1</th>
             <td>smithers</td>
             <td>54.78</td>
             <td>-127.17</td>
             <td>CA</td>
             <td>1551654000</td>
             <td></td>
             <td>26</td>
             <td>5</td>
             <td>2.62</td>
             <td>19.40</td>
           </tr>
           <tr>
             <th>2</th>
             <td>tirat karmel</td>
             <td>32.76</td>
             <td>34.97</td>
             <td>IL</td>
             <td>1551656758</td>
             <td></td>
             <td>90</td>
             <td>88</td>
             <td>1.99</td>
             <td>51.01</td>
           </tr>
           <tr>
             <th>3</th>
             <td>igrim</td>
             <td>63.19</td>
             <td>64.42</td>
             <td>RU</td>
             <td>1551657025</td>
             <td></td>
             <td>71</td>
             <td>68</td>
             <td>9.44</td>
             <td>8.23</td>
           </tr>
           <tr>
             <th>4</th>
             <td>santa rosa</td>
             <td>-36.62</td>
             <td>-64.29</td>
             <td>AR</td>
             <td>1551657027</td>
             <td></td>
             <td>90</td>
             <td>92</td>
             <td>7.87</td>
             <td>68.17</td>
           </tr>
           <tr>
             <th>5</th>
             <td>cavalcante</td>
             <td>-13.79</td>
             <td>-47.46</td>
             <td>BR</td>
             <td>1551657028</td>
             <td></td>
             <td>94</td>
             <td>64</td>
             <td>2.62</td>
             <td>68.48</td>
           </tr>
         </tbody>
       </table>
       </div>

# Latitude vs Temperature Plot


```python
sampled_cities.plot(kind="scatter", x="Lat", y="Max Temp",
                    grid=True, color="blue", edgecolor = "black", s =150, linewidth = 2, 
                    figsize =(20, 15))
plt.title('City Latitude vs. Max Temperature (12/21/17)', fontsize = 30)
plt.ylabel('Max Temperature (F)',  fontsize = 25)
plt.xlabel('Latitude', fontsize = 25)
plt.grid(True)
plt.xlim(-80, 80)
plt.ylim(-60, 120)
plt.tick_params(labelsize=20)
plt.show()
plt.savefig('latitude_temp')
```


![png](latitude_temp.png)


# Latitude vs. Humidity Plot


```python
sampled_cities.plot(kind="scatter",x="Lat",y="Humidity",
                    grid=True, color="blue", edgecolor = "black", s =150, linewidth = 2, 
                    figsize =(20, 15))
plt.title('City Latitude vs. Humidity (12/21/17)', fontsize = 30)
plt.ylabel('Humidity (%)', fontsize = 25)
plt.xlabel('Latitude', fontsize = 25)
plt.grid(True)
plt.xlim(-80, 80)
plt.ylim(-20, 120)
plt.tick_params(labelsize=20)
plt.show()
plt.savefig('latitude_humidity')
```


    <matplotlib.figure.Figure at 0x11dd53c18>



![png](latitude_humidity.png)


# Latitude vs. Cloudiness Plot


```python
sampled_cities.plot(kind="scatter",x="Lat",y="Cloudiness",
                    grid=True, color="blue", edgecolor = "black", s =150, linewidth = 2, 
                    figsize =(20, 15))
plt.title('City Latitude vs. Cloudiness (12/21/17)', fontsize = 30)
plt.ylabel('Cloudiness (%)', fontsize = 25)
plt.xlabel('Latitude', fontsize = 25)
plt.grid(True)
plt.xlim(-80, 80)
plt.ylim(-20, 120)
plt.tick_params(labelsize=20)
plt.show()
plt.savefig('latitude_cloudiness')
```


    <matplotlib.figure.Figure at 0x11e8844e0>



![png](latitude_cloudiness.png)


# Latitude vs. Wind Speed Plot


```python
sampled_cities.plot(kind="scatter",x="Lat",y="Wind Speed",
                    grid=True, color="blue", edgecolor = "black", s =150, linewidth = 2, 
                    figsize =(20, 15))
plt.title('City Latitude vs. Wind Speed (12/21/17)', fontsize = 30)
plt.ylabel('Wind Speed (mph)', fontsize = 25)
plt.xlabel('Latitude', fontsize = 25)
plt.grid(True)
plt.xlim(-80, 80)
plt.ylim(-10, 40)
plt.tick_params(labelsize=20)
plt.show()
plt.savefig('latitude_windspeed')
```


    <matplotlib.figure.Figure at 0x11e852c18>



![png](latitude_windspeed.png)

