

```python
# 3 Observations
#1:Cloudiness  variable is not dependent on latitude.
#2:Windspeed tends to decrease at the equator (by eye, didn't do stat test to see if there was a significant difference between wind speed north and south of equator vs at the equator).
#3: Temperature tends to decrease as latitude increases (Also did not do statistical testing to see if there was a significant difference to support my statement.)

```


```python
#Import Dependencies
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sb
import sys
import datetime
import requests

import openweathermapy.core as owm
from owm_api import api_key
from citipy import citipy
```


```python
Random_Locations = pd.DataFrame()
Random_Locations ['Lat_Val'] =[np.random.uniform(-90,90) for x in range(1300)]
Random_Locations ['Lng_Val'] =[np.random.uniform(-180,180) for x in range(1300)]

Random_Locations['City'] = ""
Random_Locations['Country'] = ""
Random_Locations['Temperature'] = ""
Random_Locations['Humidity'] = ""
Random_Locations['Cloudiness'] = ""
Random_Locations['Wind Speed'] = ""
count = 0

for index, row in Random_Locations.iterrows():
    near_city = citipy.nearest_city(row['Lat_Val'], row['Lng_Val']).city_name
    near_country = citipy.nearest_city(row['Lat_Val'], row['Lng_Val']).country_code
    Random_Locations.set_value(index,"City",near_city)
    Random_Locations.set_value(index,"Country",near_country)
    
Random_Locations=Random_Locations.drop_duplicates(['City','Country'],keep = "first")
    
len(Random_Locations)
```




    560




```python
Random_Locations.head(5)
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
      <th>Lat_Val</th>
      <th>Lng_Val</th>
      <th>City</th>
      <th>Country</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-86.090338</td>
      <td>2.549595</td>
      <td>hermanus</td>
      <td>za</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>-75.384644</td>
      <td>-129.760236</td>
      <td>rikitea</td>
      <td>pf</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>-75.213941</td>
      <td>-76.471090</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>19.304703</td>
      <td>-91.565217</td>
      <td>sabancuy</td>
      <td>mx</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>56.281463</td>
      <td>70.263781</td>
      <td>abatskoye</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
base_url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

record = 0
for index, row in Random_Locations.iterrows():
    city_name = row['City']
    country = row['Country']
    query_url = base_url + "appid=" + api_key + "&q=" + city_name + "&units=" + units
    print (query_url)
    try: 
        weather_response = requests.get(query_url)
        weather_json = weather_response.json()
        latitude = weather_json["coord"]["lat"]
        longitude = weather_json["coord"]["lon"]
        temp = weather_json["main"]["temp"]
        humidity = weather_json["main"]["humidity"]
        cloud = weather_json["clouds"]["all"]
        wind = weather_json["wind"]["speed"]
        Random_Locations.set_value(index,"Temperature", temp)
        Random_Locations.set_value(index,"Humidity",humidity)
        Random_Locations.set_value(index,"Wind Speed", wind)
        Random_Locations.set_value(index,"Cloudiness",cloud)
        print("Retrieved data for %s, %s" % (city_name, country))
    except: 
        print ("The error is ", sys.exc_info()[0])
        print("No data for %s, %s" % (city_name,country))
```

    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hermanus&units=imperial
    Retrieved data for hermanus, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rikitea&units=imperial
    Retrieved data for rikitea, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ushuaia&units=imperial
    Retrieved data for ushuaia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sabancuy&units=imperial
    Retrieved data for sabancuy, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=abatskoye&units=imperial
    Retrieved data for abatskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=punta arenas&units=imperial
    Retrieved data for punta arenas, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint anthony&units=imperial
    Retrieved data for saint anthony, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jamestown&units=imperial
    Retrieved data for jamestown, sh
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sterling&units=imperial
    Retrieved data for sterling, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bardsir&units=imperial
    Retrieved data for bardsir, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=orlik&units=imperial
    Retrieved data for orlik, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aklavik&units=imperial
    Retrieved data for aklavik, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=qaanaaq&units=imperial
    Retrieved data for qaanaaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santa eulalia&units=imperial
    Retrieved data for santa eulalia, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barentsburg&units=imperial
    The error is  <class 'KeyError'>
    No data for barentsburg, sj
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=provideniya&units=imperial
    Retrieved data for provideniya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ukiah&units=imperial
    Retrieved data for ukiah, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cabo rojo&units=imperial
    Retrieved data for cabo rojo, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=thompson&units=imperial
    Retrieved data for thompson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=glace bay&units=imperial
    Retrieved data for glace bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moose factory&units=imperial
    Retrieved data for moose factory, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=souillac&units=imperial
    Retrieved data for souillac, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jeremoabo&units=imperial
    Retrieved data for jeremoabo, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=albany&units=imperial
    Retrieved data for albany, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=norman wells&units=imperial
    Retrieved data for norman wells, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lephepe&units=imperial
    The error is  <class 'KeyError'>
    No data for lephepe, bw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=avarua&units=imperial
    Retrieved data for avarua, ck
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arraial do cabo&units=imperial
    Retrieved data for arraial do cabo, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tiznit&units=imperial
    Retrieved data for tiznit, ma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-pierre&units=imperial
    Retrieved data for saint-pierre, re
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=carnarvon&units=imperial
    Retrieved data for carnarvon, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=iranshahr&units=imperial
    Retrieved data for iranshahr, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=temaraia&units=imperial
    The error is  <class 'KeyError'>
    No data for temaraia, ki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=balad&units=imperial
    Retrieved data for balad, iq
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alakurtti&units=imperial
    Retrieved data for alakurtti, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=busselton&units=imperial
    Retrieved data for busselton, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yeppoon&units=imperial
    Retrieved data for yeppoon, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=east london&units=imperial
    Retrieved data for east london, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-philippe&units=imperial
    Retrieved data for saint-philippe, re
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=miranorte&units=imperial
    The error is  <class 'KeyError'>
    No data for miranorte, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=memari&units=imperial
    Retrieved data for memari, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint george&units=imperial
    Retrieved data for saint george, bm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vaini&units=imperial
    Retrieved data for vaini, to
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=taolanaro&units=imperial
    The error is  <class 'KeyError'>
    No data for taolanaro, mg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hithadhoo&units=imperial
    Retrieved data for hithadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moron&units=imperial
    Retrieved data for moron, mn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=castro&units=imperial
    Retrieved data for castro, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bluff&units=imperial
    Retrieved data for bluff, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tiksi&units=imperial
    Retrieved data for tiksi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ponta do sol&units=imperial
    Retrieved data for ponta do sol, cv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bredasdorp&units=imperial
    Retrieved data for bredasdorp, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barra dos coqueiros&units=imperial
    Retrieved data for barra dos coqueiros, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tasiilaq&units=imperial
    Retrieved data for tasiilaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=acarau&units=imperial
    The error is  <class 'KeyError'>
    No data for acarau, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saskylakh&units=imperial
    Retrieved data for saskylakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bambous virieux&units=imperial
    Retrieved data for bambous virieux, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pepperell&units=imperial
    Retrieved data for pepperell, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yendi&units=imperial
    Retrieved data for yendi, gh
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vaitupu&units=imperial
    The error is  <class 'KeyError'>
    No data for vaitupu, wf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=antofagasta&units=imperial
    Retrieved data for antofagasta, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kysyl-syr&units=imperial
    Retrieved data for kysyl-syr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chokurdakh&units=imperial
    Retrieved data for chokurdakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yar-sale&units=imperial
    Retrieved data for yar-sale, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=stornoway&units=imperial
    The error is  <class 'KeyError'>
    No data for stornoway, gb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dikson&units=imperial
    Retrieved data for dikson, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port hardy&units=imperial
    Retrieved data for port hardy, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=katsuura&units=imperial
    Retrieved data for katsuura, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yellowknife&units=imperial
    Retrieved data for yellowknife, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zanatepec&units=imperial
    The error is  <class 'KeyError'>
    No data for zanatepec, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port hawkesbury&units=imperial
    Retrieved data for port hawkesbury, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=buala&units=imperial
    Retrieved data for buala, sb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=katghora&units=imperial
    Retrieved data for katghora, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pinotepa nacional&units=imperial
    The error is  <class 'KeyError'>
    No data for pinotepa nacional, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=omboue&units=imperial
    Retrieved data for omboue, ga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nizhneyansk&units=imperial
    The error is  <class 'KeyError'>
    No data for nizhneyansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vila velha&units=imperial
    Retrieved data for vila velha, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=porto novo&units=imperial
    Retrieved data for porto novo, cv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tuktoyaktuk&units=imperial
    Retrieved data for tuktoyaktuk, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hilo&units=imperial
    Retrieved data for hilo, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ponta delgada&units=imperial
    Retrieved data for ponta delgada, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=grindavik&units=imperial
    Retrieved data for grindavik, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kapaa&units=imperial
    Retrieved data for kapaa, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cape town&units=imperial
    Retrieved data for cape town, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mehamn&units=imperial
    Retrieved data for mehamn, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ocos&units=imperial
    Retrieved data for ocos, gt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lac-megantic&units=imperial
    Retrieved data for lac-megantic, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port macquarie&units=imperial
    Retrieved data for port macquarie, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=torbay&units=imperial
    Retrieved data for torbay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=denizli&units=imperial
    Retrieved data for denizli, tr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=torres&units=imperial
    Retrieved data for torres, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ordzhonikidze&units=imperial
    Retrieved data for ordzhonikidze, ua
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kaitangata&units=imperial
    Retrieved data for kaitangata, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port blair&units=imperial
    Retrieved data for port blair, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nome&units=imperial
    Retrieved data for nome, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chara&units=imperial
    Retrieved data for chara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nikolskoye&units=imperial
    Retrieved data for nikolskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yumen&units=imperial
    Retrieved data for yumen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=iqaluit&units=imperial
    Retrieved data for iqaluit, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=narsaq&units=imperial
    Retrieved data for narsaq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=husavik&units=imperial
    Retrieved data for husavik, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=faanui&units=imperial
    Retrieved data for faanui, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=isangel&units=imperial
    Retrieved data for isangel, vu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tsihombe&units=imperial
    The error is  <class 'KeyError'>
    No data for tsihombe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=urubamba&units=imperial
    Retrieved data for urubamba, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rudnichnyy&units=imperial
    Retrieved data for rudnichnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=eydhafushi&units=imperial
    Retrieved data for eydhafushi, mv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bairiki&units=imperial
    The error is  <class 'KeyError'>
    No data for bairiki, ki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=punta alta&units=imperial
    Retrieved data for punta alta, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-joseph&units=imperial
    Retrieved data for saint-joseph, re
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mys shmidta&units=imperial
    The error is  <class 'KeyError'>
    No data for mys shmidta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hambantota&units=imperial
    Retrieved data for hambantota, lk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=polovinnoye&units=imperial
    Retrieved data for polovinnoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=marsa matruh&units=imperial
    Retrieved data for marsa matruh, eg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=coquimbo&units=imperial
    Retrieved data for coquimbo, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=upernavik&units=imperial
    Retrieved data for upernavik, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chuy&units=imperial
    Retrieved data for chuy, uy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=illoqqortoormiut&units=imperial
    The error is  <class 'KeyError'>
    No data for illoqqortoormiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mahon&units=imperial
    Retrieved data for mahon, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ribeira grande&units=imperial
    Retrieved data for ribeira grande, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=takestan&units=imperial
    Retrieved data for takestan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=longyearbyen&units=imperial
    Retrieved data for longyearbyen, sj
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=makakilo city&units=imperial
    Retrieved data for makakilo city, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kavieng&units=imperial
    Retrieved data for kavieng, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barrow&units=imperial
    Retrieved data for barrow, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=llaillay&units=imperial
    Retrieved data for llaillay, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sorland&units=imperial
    Retrieved data for sorland, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tuatapere&units=imperial
    Retrieved data for tuatapere, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zhigansk&units=imperial
    Retrieved data for zhigansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=atuona&units=imperial
    Retrieved data for atuona, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mayo&units=imperial
    Retrieved data for mayo, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=xudat&units=imperial
    Retrieved data for xudat, az
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aykhal&units=imperial
    Retrieved data for aykhal, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cherskiy&units=imperial
    Retrieved data for cherskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hobart&units=imperial
    Retrieved data for hobart, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=haines junction&units=imperial
    Retrieved data for haines junction, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mullaitivu&units=imperial
    The error is  <class 'KeyError'>
    No data for mullaitivu, lk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=grand gaube&units=imperial
    Retrieved data for grand gaube, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gat&units=imperial
    Retrieved data for gat, ly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=anadyr&units=imperial
    Retrieved data for anadyr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=buraydah&units=imperial
    Retrieved data for buraydah, sa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hami&units=imperial
    Retrieved data for hami, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lorengau&units=imperial
    Retrieved data for lorengau, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kumano&units=imperial
    Retrieved data for kumano, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mataura&units=imperial
    Retrieved data for mataura, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=comodoro rivadavia&units=imperial
    Retrieved data for comodoro rivadavia, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bandarban&units=imperial
    Retrieved data for bandarban, bd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=berlevag&units=imperial
    Retrieved data for berlevag, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=verin&units=imperial
    Retrieved data for verin, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=esperance&units=imperial
    Retrieved data for esperance, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chapais&units=imperial
    Retrieved data for chapais, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=puerto ayora&units=imperial
    Retrieved data for puerto ayora, ec
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tura&units=imperial
    Retrieved data for tura, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lagoa&units=imperial
    Retrieved data for lagoa, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=panguna&units=imperial
    Retrieved data for panguna, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=toliary&units=imperial
    The error is  <class 'KeyError'>
    No data for toliary, mg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sapao&units=imperial
    The error is  <class 'KeyError'>
    No data for sapao, ph
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port alfred&units=imperial
    Retrieved data for port alfred, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nabire&units=imperial
    Retrieved data for nabire, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bengkulu&units=imperial
    The error is  <class 'KeyError'>
    No data for bengkulu, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kendari&units=imperial
    Retrieved data for kendari, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=leningradskiy&units=imperial
    Retrieved data for leningradskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dordrecht&units=imperial
    Retrieved data for dordrecht, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=qingyuan&units=imperial
    Retrieved data for qingyuan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=asti&units=imperial
    Retrieved data for asti, it
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=namatanai&units=imperial
    Retrieved data for namatanai, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yaring&units=imperial
    Retrieved data for yaring, th
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=samusu&units=imperial
    The error is  <class 'KeyError'>
    No data for samusu, ws
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yuancheng&units=imperial
    The error is  <class 'KeyError'>
    No data for yuancheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=strelca&units=imperial
    The error is  <class 'KeyError'>
    No data for strelca, bg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maragogi&units=imperial
    Retrieved data for maragogi, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kodiak&units=imperial
    Retrieved data for kodiak, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mahebourg&units=imperial
    Retrieved data for mahebourg, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=udachnyy&units=imperial
    Retrieved data for udachnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cangzhou&units=imperial
    Retrieved data for cangzhou, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=curanilahue&units=imperial
    Retrieved data for curanilahue, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=moen&units=imperial
    Retrieved data for moen, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vostok&units=imperial
    Retrieved data for vostok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aflu&units=imperial
    The error is  <class 'KeyError'>
    No data for aflu, dz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kailua&units=imperial
    Retrieved data for kailua, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ilulissat&units=imperial
    Retrieved data for ilulissat, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=college&units=imperial
    Retrieved data for college, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=namibe&units=imperial
    Retrieved data for namibe, ao
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=new norfolk&units=imperial
    Retrieved data for new norfolk, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cartagena&units=imperial
    Retrieved data for cartagena, co
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=trairi&units=imperial
    Retrieved data for trairi, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=peniche&units=imperial
    Retrieved data for peniche, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=budureasa&units=imperial
    Retrieved data for budureasa, ro
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sorkjosen&units=imperial
    The error is  <class 'KeyError'>
    No data for sorkjosen, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tadine&units=imperial
    Retrieved data for tadine, nc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shepsi&units=imperial
    Retrieved data for shepsi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kayerkan&units=imperial
    Retrieved data for kayerkan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=egvekinot&units=imperial
    Retrieved data for egvekinot, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=victoria&units=imperial
    Retrieved data for victoria, sc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mbandaka&units=imperial
    Retrieved data for mbandaka, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=palmer&units=imperial
    Retrieved data for palmer, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=palabuhanratu&units=imperial
    The error is  <class 'KeyError'>
    No data for palabuhanratu, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=salalah&units=imperial
    Retrieved data for salalah, om
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=codrington&units=imperial
    Retrieved data for codrington, ag
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sentyabrskiy&units=imperial
    The error is  <class 'KeyError'>
    No data for sentyabrskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fevralsk&units=imperial
    The error is  <class 'KeyError'>
    No data for fevralsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rawannawi&units=imperial
    The error is  <class 'KeyError'>
    No data for rawannawi, ki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=choucheng&units=imperial
    The error is  <class 'KeyError'>
    No data for choucheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=acapulco&units=imperial
    Retrieved data for acapulco, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rio gallegos&units=imperial
    Retrieved data for rio gallegos, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=slavuta&units=imperial
    Retrieved data for slavuta, ua
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=north platte&units=imperial
    Retrieved data for north platte, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nador&units=imperial
    Retrieved data for nador, ma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lulea&units=imperial
    Retrieved data for lulea, se
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kirakira&units=imperial
    Retrieved data for kirakira, sb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=marcona&units=imperial
    The error is  <class 'KeyError'>
    No data for marcona, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kununurra&units=imperial
    Retrieved data for kununurra, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=esil&units=imperial
    Retrieved data for esil, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hay river&units=imperial
    Retrieved data for hay river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mar del plata&units=imperial
    Retrieved data for mar del plata, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=muros&units=imperial
    Retrieved data for muros, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ngunguru&units=imperial
    Retrieved data for ngunguru, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pitimbu&units=imperial
    Retrieved data for pitimbu, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=butaritari&units=imperial
    Retrieved data for butaritari, ki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yulara&units=imperial
    Retrieved data for yulara, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=viloco&units=imperial
    Retrieved data for viloco, bo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=breytovo&units=imperial
    Retrieved data for breytovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=camacha&units=imperial
    Retrieved data for camacha, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pasni&units=imperial
    Retrieved data for pasni, pk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dingle&units=imperial
    Retrieved data for dingle, ie
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=klaeng&units=imperial
    Retrieved data for klaeng, th
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cockburn town&units=imperial
    Retrieved data for cockburn town, tc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vrangel&units=imperial
    Retrieved data for vrangel, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=machali&units=imperial
    The error is  <class 'KeyError'>
    No data for machali, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dukat&units=imperial
    Retrieved data for dukat, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=manavalakurichi&units=imperial
    Retrieved data for manavalakurichi, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kualakapuas&units=imperial
    Retrieved data for kualakapuas, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shimoda&units=imperial
    Retrieved data for shimoda, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nanortalik&units=imperial
    Retrieved data for nanortalik, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=palaiokhora&units=imperial
    The error is  <class 'KeyError'>
    No data for palaiokhora, gr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kasongo-lunda&units=imperial
    Retrieved data for kasongo-lunda, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aptos&units=imperial
    Retrieved data for aptos, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=eyl&units=imperial
    Retrieved data for eyl, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cabo san lucas&units=imperial
    Retrieved data for cabo san lucas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lebu&units=imperial
    Retrieved data for lebu, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santa rosa&units=imperial
    Retrieved data for santa rosa, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sibu&units=imperial
    Retrieved data for sibu, my
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sitka&units=imperial
    Retrieved data for sitka, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=serenje&units=imperial
    Retrieved data for serenje, zm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=garowe&units=imperial
    Retrieved data for garowe, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=olafsvik&units=imperial
    The error is  <class 'KeyError'>
    No data for olafsvik, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vaitape&units=imperial
    Retrieved data for vaitape, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kloulklubed&units=imperial
    Retrieved data for kloulklubed, pw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=paengaroa&units=imperial
    Retrieved data for paengaroa, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kipushi&units=imperial
    Retrieved data for kipushi, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port elizabeth&units=imperial
    Retrieved data for port elizabeth, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rocha&units=imperial
    Retrieved data for rocha, uy
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=porto seguro&units=imperial
    Retrieved data for porto seguro, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dunedin&units=imperial
    Retrieved data for dunedin, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=guerrero negro&units=imperial
    Retrieved data for guerrero negro, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ciudad bolivar&units=imperial
    Retrieved data for ciudad bolivar, ve
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=miraflores&units=imperial
    Retrieved data for miraflores, co
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kerman&units=imperial
    Retrieved data for kerman, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=goderich&units=imperial
    Retrieved data for goderich, sl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alta floresta&units=imperial
    Retrieved data for alta floresta, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kruisfontein&units=imperial
    Retrieved data for kruisfontein, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bethel&units=imperial
    Retrieved data for bethel, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=airai&units=imperial
    Retrieved data for airai, pw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=madison&units=imperial
    Retrieved data for madison, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=broken hill&units=imperial
    Retrieved data for broken hill, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kongsvinger&units=imperial
    Retrieved data for kongsvinger, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=te anau&units=imperial
    Retrieved data for te anau, nz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=attawapiskat&units=imperial
    The error is  <class 'KeyError'>
    No data for attawapiskat, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bosaso&units=imperial
    Retrieved data for bosaso, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santa lucia&units=imperial
    Retrieved data for santa lucia, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bandarbeyla&units=imperial
    Retrieved data for bandarbeyla, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vanimo&units=imperial
    Retrieved data for vanimo, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vestmannaeyjar&units=imperial
    Retrieved data for vestmannaeyjar, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=colac&units=imperial
    Retrieved data for colac, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san luis&units=imperial
    Retrieved data for san luis, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mecca&units=imperial
    Retrieved data for mecca, sa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sobolevo&units=imperial
    Retrieved data for sobolevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kamen-na-obi&units=imperial
    Retrieved data for kamen-na-obi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chernyshevskiy&units=imperial
    Retrieved data for chernyshevskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mount pleasant&units=imperial
    Retrieved data for mount pleasant, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=riyadh&units=imperial
    Retrieved data for riyadh, sa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=severo-kurilsk&units=imperial
    Retrieved data for severo-kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=viligili&units=imperial
    The error is  <class 'KeyError'>
    No data for viligili, mv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=touros&units=imperial
    Retrieved data for touros, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bilma&units=imperial
    Retrieved data for bilma, ne
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=qaqortoq&units=imperial
    Retrieved data for qaqortoq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sokoni&units=imperial
    Retrieved data for sokoni, tz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chavakkad&units=imperial
    Retrieved data for chavakkad, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao filipe&units=imperial
    Retrieved data for sao filipe, cv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=batagay-alyta&units=imperial
    Retrieved data for batagay-alyta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jacmel&units=imperial
    Retrieved data for jacmel, ht
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bakersfield&units=imperial
    Retrieved data for bakersfield, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=adrar&units=imperial
    Retrieved data for adrar, dz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bargal&units=imperial
    The error is  <class 'KeyError'>
    No data for bargal, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vardo&units=imperial
    Retrieved data for vardo, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pascagoula&units=imperial
    Retrieved data for pascagoula, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wodonga&units=imperial
    Retrieved data for wodonga, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=belushya guba&units=imperial
    The error is  <class 'KeyError'>
    No data for belushya guba, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pisco&units=imperial
    Retrieved data for pisco, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ndende&units=imperial
    The error is  <class 'KeyError'>
    No data for ndende, ga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=atasu&units=imperial
    Retrieved data for atasu, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yaan&units=imperial
    Retrieved data for yaan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=la palma&units=imperial
    Retrieved data for la palma, pa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tahta&units=imperial
    The error is  <class 'KeyError'>
    No data for tahta, eg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port lincoln&units=imperial
    Retrieved data for port lincoln, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sikonge&units=imperial
    Retrieved data for sikonge, tz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amderma&units=imperial
    The error is  <class 'KeyError'>
    No data for amderma, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rock sound&units=imperial
    Retrieved data for rock sound, bs
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nuuk&units=imperial
    Retrieved data for nuuk, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rio grande&units=imperial
    Retrieved data for rio grande, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=princeton&units=imperial
    Retrieved data for princeton, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wazzan&units=imperial
    The error is  <class 'KeyError'>
    No data for wazzan, ma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=coulihaut&units=imperial
    The error is  <class 'KeyError'>
    No data for coulihaut, dm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=george town&units=imperial
    Retrieved data for george town, ky
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kholmogory&units=imperial
    Retrieved data for kholmogory, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chenghai&units=imperial
    Retrieved data for chenghai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=novobirilyussy&units=imperial
    Retrieved data for novobirilyussy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=praia&units=imperial
    Retrieved data for praia, cv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=armacao dos buzios&units=imperial
    The error is  <class 'KeyError'>
    No data for armacao dos buzios, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=georgetown&units=imperial
    Retrieved data for georgetown, sh
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san jeronimo&units=imperial
    Retrieved data for san jeronimo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aksu&units=imperial
    Retrieved data for aksu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yarmouth&units=imperial
    Retrieved data for yarmouth, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=paamiut&units=imperial
    Retrieved data for paamiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hirara&units=imperial
    Retrieved data for hirara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hofn&units=imperial
    Retrieved data for hofn, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hobyo&units=imperial
    Retrieved data for hobyo, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=olinda&units=imperial
    Retrieved data for olinda, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maloshuyka&units=imperial
    The error is  <class 'KeyError'>
    No data for maloshuyka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=karamursel&units=imperial
    Retrieved data for karamursel, tr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=carballo&units=imperial
    Retrieved data for carballo, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bozuyuk&units=imperial
    Retrieved data for bozuyuk, tr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=puerto escondido&units=imperial
    Retrieved data for puerto escondido, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lyubinskiy&units=imperial
    Retrieved data for lyubinskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ilebo&units=imperial
    Retrieved data for ilebo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sinegorskiy&units=imperial
    Retrieved data for sinegorskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tonj&units=imperial
    The error is  <class 'KeyError'>
    No data for tonj, sd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nguiu&units=imperial
    The error is  <class 'KeyError'>
    No data for nguiu, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wagga wagga&units=imperial
    Retrieved data for wagga wagga, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alghero&units=imperial
    Retrieved data for alghero, it
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san patricio&units=imperial
    Retrieved data for san patricio, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=meulaboh&units=imperial
    Retrieved data for meulaboh, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=grand river south east&units=imperial
    The error is  <class 'KeyError'>
    No data for grand river south east, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nioro&units=imperial
    Retrieved data for nioro, ml
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hamilton&units=imperial
    Retrieved data for hamilton, bm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mount isa&units=imperial
    Retrieved data for mount isa, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fez&units=imperial
    Retrieved data for fez, ma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=matara&units=imperial
    Retrieved data for matara, lk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=charters towers&units=imperial
    Retrieved data for charters towers, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dombarovskiy&units=imperial
    Retrieved data for dombarovskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mareeba&units=imperial
    Retrieved data for mareeba, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=quelimane&units=imperial
    Retrieved data for quelimane, mz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lakes entrance&units=imperial
    Retrieved data for lakes entrance, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=clifton springs&units=imperial
    Retrieved data for clifton springs, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=french harbor&units=imperial
    Retrieved data for french harbor, hn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=geresk&units=imperial
    The error is  <class 'KeyError'>
    No data for geresk, af
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=erenhot&units=imperial
    Retrieved data for erenhot, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sioux lookout&units=imperial
    Retrieved data for sioux lookout, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=onega&units=imperial
    Retrieved data for onega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=asau&units=imperial
    The error is  <class 'KeyError'>
    No data for asau, tv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gorontalo&units=imperial
    Retrieved data for gorontalo, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=xining&units=imperial
    Retrieved data for xining, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=marawi&units=imperial
    Retrieved data for marawi, sd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tsagan aman&units=imperial
    Retrieved data for tsagan aman, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ngukurr&units=imperial
    The error is  <class 'KeyError'>
    No data for ngukurr, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=makinsk&units=imperial
    Retrieved data for makinsk, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tikhvin&units=imperial
    Retrieved data for tikhvin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vanavara&units=imperial
    Retrieved data for vanavara, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=walvis bay&units=imperial
    Retrieved data for walvis bay, na
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alofi&units=imperial
    Retrieved data for alofi, nu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao felix do xingu&units=imperial
    Retrieved data for sao felix do xingu, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=big spring&units=imperial
    Retrieved data for big spring, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=praia da vitoria&units=imperial
    Retrieved data for praia da vitoria, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=binga&units=imperial
    Retrieved data for binga, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=laguna&units=imperial
    Retrieved data for laguna, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san jose&units=imperial
    Retrieved data for san jose, bo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kahului&units=imperial
    Retrieved data for kahului, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kumluca&units=imperial
    Retrieved data for kumluca, tr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san ignacio&units=imperial
    Retrieved data for san ignacio, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=petropavlovsk-kamchatskiy&units=imperial
    Retrieved data for petropavlovsk-kamchatskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=madang&units=imperial
    Retrieved data for madang, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san matias&units=imperial
    Retrieved data for san matias, bo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nelson bay&units=imperial
    Retrieved data for nelson bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=manado&units=imperial
    Retrieved data for manado, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=okhotsk&units=imperial
    Retrieved data for okhotsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=atikokan&units=imperial
    Retrieved data for atikokan, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=plettenberg bay&units=imperial
    Retrieved data for plettenberg bay, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=samalaeulu&units=imperial
    The error is  <class 'KeyError'>
    No data for samalaeulu, ws
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ornskoldsvik&units=imperial
    Retrieved data for ornskoldsvik, se
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao jose da coroa grande&units=imperial
    Retrieved data for sao jose da coroa grande, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=khatanga&units=imperial
    Retrieved data for khatanga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yamada&units=imperial
    Retrieved data for yamada, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wahran&units=imperial
    The error is  <class 'KeyError'>
    No data for wahran, dz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=blairmore&units=imperial
    Retrieved data for blairmore, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chaves&units=imperial
    Retrieved data for chaves, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shushtar&units=imperial
    Retrieved data for shushtar, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=baruun-urt&units=imperial
    Retrieved data for baruun-urt, mn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=dhidhdhoo&units=imperial
    Retrieved data for dhidhdhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hervey bay&units=imperial
    Retrieved data for hervey bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=varnavino&units=imperial
    Retrieved data for varnavino, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=umzimvubu&units=imperial
    The error is  <class 'KeyError'>
    No data for umzimvubu, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nanyang&units=imperial
    Retrieved data for nanyang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fortuna&units=imperial
    Retrieved data for fortuna, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=geraldton&units=imperial
    Retrieved data for geraldton, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bafq&units=imperial
    Retrieved data for bafq, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=khalkis&units=imperial
    The error is  <class 'KeyError'>
    No data for khalkis, gr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=los llanos de aridane&units=imperial
    Retrieved data for los llanos de aridane, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=richards bay&units=imperial
    Retrieved data for richards bay, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ntchisi&units=imperial
    Retrieved data for ntchisi, mw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sisimiut&units=imperial
    Retrieved data for sisimiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=broome&units=imperial
    Retrieved data for broome, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=uitenhage&units=imperial
    Retrieved data for uitenhage, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pochinki&units=imperial
    Retrieved data for pochinki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cidreira&units=imperial
    Retrieved data for cidreira, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=victor harbor&units=imperial
    Retrieved data for victor harbor, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=yuzhnyy&units=imperial
    Retrieved data for yuzhnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=susaki&units=imperial
    Retrieved data for susaki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ancud&units=imperial
    Retrieved data for ancud, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kalmunai&units=imperial
    Retrieved data for kalmunai, lk
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lloydminster&units=imperial
    Retrieved data for lloydminster, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=talnakh&units=imperial
    Retrieved data for talnakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lingao&units=imperial
    Retrieved data for lingao, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=amapa&units=imperial
    Retrieved data for amapa, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=hasaki&units=imperial
    Retrieved data for hasaki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shonguy&units=imperial
    Retrieved data for shonguy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bathsheba&units=imperial
    Retrieved data for bathsheba, bb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port-gentil&units=imperial
    Retrieved data for port-gentil, ga
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jiexiu&units=imperial
    Retrieved data for jiexiu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=fairbanks&units=imperial
    Retrieved data for fairbanks, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wooster&units=imperial
    Retrieved data for wooster, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nyrob&units=imperial
    Retrieved data for nyrob, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bandar-e lengeh&units=imperial
    Retrieved data for bandar-e lengeh, ir
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pangnirtung&units=imperial
    Retrieved data for pangnirtung, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=namwala&units=imperial
    Retrieved data for namwala, zm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maxixe&units=imperial
    Retrieved data for maxixe, mz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=matelandia&units=imperial
    Retrieved data for matelandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=shimoga&units=imperial
    Retrieved data for shimoga, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bebra&units=imperial
    Retrieved data for bebra, de
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=martinsville&units=imperial
    Retrieved data for martinsville, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=asyut&units=imperial
    Retrieved data for asyut, eg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sataua&units=imperial
    The error is  <class 'KeyError'>
    No data for sataua, ws
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=aktau&units=imperial
    Retrieved data for aktau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vestmanna&units=imperial
    Retrieved data for vestmanna, fo
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=faya&units=imperial
    Retrieved data for faya, td
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pokhara&units=imperial
    Retrieved data for pokhara, np
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gravdal&units=imperial
    Retrieved data for gravdal, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=henties bay&units=imperial
    Retrieved data for henties bay, na
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=high level&units=imperial
    Retrieved data for high level, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mutsamudu&units=imperial
    The error is  <class 'KeyError'>
    No data for mutsamudu, km
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cap malheureux&units=imperial
    Retrieved data for cap malheureux, mu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nizwa&units=imperial
    Retrieved data for nizwa, om
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=miles city&units=imperial
    Retrieved data for miles city, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=barawe&units=imperial
    The error is  <class 'KeyError'>
    No data for barawe, so
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san blas&units=imperial
    Retrieved data for san blas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mansa&units=imperial
    Retrieved data for mansa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sao joao da barra&units=imperial
    Retrieved data for sao joao da barra, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tuni&units=imperial
    Retrieved data for tuni, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=coihaique&units=imperial
    Retrieved data for coihaique, cl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ust-kan&units=imperial
    Retrieved data for ust-kan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=inderborskiy&units=imperial
    The error is  <class 'KeyError'>
    No data for inderborskiy, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bonthe&units=imperial
    Retrieved data for bonthe, sl
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mitsamiouli&units=imperial
    Retrieved data for mitsamiouli, km
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jalu&units=imperial
    Retrieved data for jalu, ly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lerwick&units=imperial
    Retrieved data for lerwick, gb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=havre-saint-pierre&units=imperial
    Retrieved data for havre-saint-pierre, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rabo de peixe&units=imperial
    Retrieved data for rabo de peixe, pt
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=banmo&units=imperial
    The error is  <class 'KeyError'>
    No data for banmo, mm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=anchorage&units=imperial
    Retrieved data for anchorage, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=galiwinku&units=imperial
    The error is  <class 'KeyError'>
    No data for galiwinku, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rawson&units=imperial
    Retrieved data for rawson, ar
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=blonduos&units=imperial
    The error is  <class 'KeyError'>
    No data for blonduos, is
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=atar&units=imperial
    Retrieved data for atar, mr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kuala terengganu&units=imperial
    Retrieved data for kuala terengganu, my
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=rosetta&units=imperial
    Retrieved data for rosetta, eg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sinnamary&units=imperial
    Retrieved data for sinnamary, gf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=roald&units=imperial
    Retrieved data for roald, no
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mount gambier&units=imperial
    Retrieved data for mount gambier, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wanning&units=imperial
    Retrieved data for wanning, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nhulunbuy&units=imperial
    Retrieved data for nhulunbuy, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maumere&units=imperial
    Retrieved data for maumere, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=jasper&units=imperial
    Retrieved data for jasper, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=manzanillo&units=imperial
    Retrieved data for manzanillo, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kargapolye&units=imperial
    The error is  <class 'KeyError'>
    No data for kargapolye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cockburn town&units=imperial
    Retrieved data for cockburn town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=balykshi&units=imperial
    The error is  <class 'KeyError'>
    No data for balykshi, kz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=port hedland&units=imperial
    Retrieved data for port hedland, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sola&units=imperial
    Retrieved data for sola, vu
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tigil&units=imperial
    Retrieved data for tigil, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=cosala&units=imperial
    The error is  <class 'KeyError'>
    No data for cosala, mx
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=okhtyrka&units=imperial
    Retrieved data for okhtyrka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mwense&units=imperial
    Retrieved data for mwense, zm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=the valley&units=imperial
    Retrieved data for the valley, ai
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lavrentiya&units=imperial
    Retrieved data for lavrentiya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tabiauea&units=imperial
    The error is  <class 'KeyError'>
    No data for tabiauea, ki
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ahuimanu&units=imperial
    Retrieved data for ahuimanu, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=gurskoye&units=imperial
    The error is  <class 'KeyError'>
    No data for gurskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=valera&units=imperial
    Retrieved data for valera, ve
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=almaznyy&units=imperial
    Retrieved data for almaznyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lolua&units=imperial
    The error is  <class 'KeyError'>
    No data for lolua, tv
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=marrakesh&units=imperial
    Retrieved data for marrakesh, ma
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=axim&units=imperial
    Retrieved data for axim, gh
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=north bend&units=imperial
    Retrieved data for north bend, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oranjemund&units=imperial
    Retrieved data for oranjemund, na
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tyup&units=imperial
    Retrieved data for tyup, kg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=papetoai&units=imperial
    Retrieved data for papetoai, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pontianak&units=imperial
    Retrieved data for pontianak, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=nagato&units=imperial
    Retrieved data for nagato, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=penzance&units=imperial
    Retrieved data for penzance, gb
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san cristobal&units=imperial
    Retrieved data for san cristobal, ec
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=arman&units=imperial
    Retrieved data for arman, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=oga&units=imperial
    Retrieved data for oga, jp
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tanjong sepat&units=imperial
    The error is  <class 'KeyError'>
    No data for tanjong sepat, my
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=vao&units=imperial
    Retrieved data for vao, nc
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=veraval&units=imperial
    Retrieved data for veraval, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=azuaga&units=imperial
    Retrieved data for azuaga, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=zhuhai&units=imperial
    Retrieved data for zhuhai, cn
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ossora&units=imperial
    Retrieved data for ossora, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tsaratanana&units=imperial
    Retrieved data for tsaratanana, mg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=akyab&units=imperial
    The error is  <class 'KeyError'>
    No data for akyab, mm
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=najran&units=imperial
    Retrieved data for najran, sa
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=coolum beach&units=imperial
    Retrieved data for coolum beach, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=besikduzu&units=imperial
    Retrieved data for besikduzu, tr
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=knysna&units=imperial
    Retrieved data for knysna, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=puerto colombia&units=imperial
    Retrieved data for puerto colombia, co
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=panama city&units=imperial
    Retrieved data for panama city, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mackenzie&units=imperial
    Retrieved data for mackenzie, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=taoudenni&units=imperial
    Retrieved data for taoudenni, ml
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saint-augustin&units=imperial
    Retrieved data for saint-augustin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tautira&units=imperial
    Retrieved data for tautira, pf
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kasongo&units=imperial
    Retrieved data for kasongo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=lady lake&units=imperial
    Retrieved data for lady lake, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=alugan&units=imperial
    Retrieved data for alugan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=la ronge&units=imperial
    Retrieved data for la ronge, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=santa eulalia del rio&units=imperial
    The error is  <class 'KeyError'>
    No data for santa eulalia del rio, es
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=ban nahin&units=imperial
    Retrieved data for ban nahin, la
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=margate&units=imperial
    Retrieved data for margate, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chiredzi&units=imperial
    Retrieved data for chiredzi, zw
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=wewak&units=imperial
    Retrieved data for wewak, pg
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=chortomlyk&units=imperial
    Retrieved data for chortomlyk, ua
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=clyde river&units=imperial
    Retrieved data for clyde river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=luderitz&units=imperial
    Retrieved data for luderitz, na
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=bireun&units=imperial
    Retrieved data for bireun, id
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=tynda&units=imperial
    Retrieved data for tynda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=huarmey&units=imperial
    Retrieved data for huarmey, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pinega&units=imperial
    Retrieved data for pinega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=caravelas&units=imperial
    Retrieved data for caravelas, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=uruzgan&units=imperial
    Retrieved data for uruzgan, af
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=sabratah&units=imperial
    Retrieved data for sabratah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=prainha&units=imperial
    Retrieved data for prainha, br
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=kungurtug&units=imperial
    Retrieved data for kungurtug, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=marietta&units=imperial
    Retrieved data for marietta, us
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=saldanha&units=imperial
    Retrieved data for saldanha, za
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=beira&units=imperial
    Retrieved data for beira, mz
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=maryborough&units=imperial
    Retrieved data for maryborough, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=labytnangi&units=imperial
    Retrieved data for labytnangi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=manjeshwar&units=imperial
    Retrieved data for manjeshwar, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=san andres&units=imperial
    Retrieved data for san andres, co
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=leh&units=imperial
    Retrieved data for leh, in
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=burnie&units=imperial
    Retrieved data for burnie, au
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=mancora&units=imperial
    Retrieved data for mancora, pe
    http://api.openweathermap.org/data/2.5/weather?appid=25bc90a1196e6f153eece0bc0b0fc9eb&q=pucallpa&units=imperial
    Retrieved data for pucallpa, pe



```python
Random_Locations.head(5)
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
      <th>Lat_Val</th>
      <th>Lng_Val</th>
      <th>City</th>
      <th>Country</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-86.090338</td>
      <td>2.549595</td>
      <td>hermanus</td>
      <td>za</td>
      <td>52.56</td>
      <td>89</td>
      <td>0</td>
      <td>4.41</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-75.384644</td>
      <td>-129.760236</td>
      <td>rikitea</td>
      <td>pf</td>
      <td>80.05</td>
      <td>100</td>
      <td>92</td>
      <td>13.35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-75.213941</td>
      <td>-76.471090</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td>41</td>
      <td>86</td>
      <td>90</td>
      <td>14.99</td>
    </tr>
    <tr>
      <th>3</th>
      <td>19.304703</td>
      <td>-91.565217</td>
      <td>sabancuy</td>
      <td>mx</td>
      <td>84.24</td>
      <td>81</td>
      <td>0</td>
      <td>9.22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>56.281463</td>
      <td>70.263781</td>
      <td>abatskoye</td>
      <td>ru</td>
      <td>29.38</td>
      <td>92</td>
      <td>88</td>
      <td>12.57</td>
    </tr>
  </tbody>
</table>
</div>




```python
Random_Locations.to_csv("Hw6_data.csv", encoding="utf-8", index=False)
```


```python
#Temperature VS LAT

for index, row in Random_Locations.iterrows():
    lat_val = Random_Locations.get_value(index, "Lat_Val")
    temp = Random_Locations.get_value(index, "Temperature")
    if (temp != ""): #some cities don't exist in openweather -sadface-
        plt.scatter(lat_val, temp, edgecolor="white", linewidths=1, marker="o",alpha=0.8, label="Cities")

now = datetime.datetime.now()
month = now.strftime("%b")
plt.title(f"City Latitude vs. Max Temperature {now.day} {month} {now.year}")
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim(-100,100)
plt.ylim(-100,110)

plt.savefig("LattemoPlt.png")
plt.show()

### its not blue like in the example but I like this better.
```


![png](output_7_0.png)



```python
#Humidity VS LAT
for index, row in Random_Locations.iterrows():
    lat_val = Random_Locations.get_value(index, "Lat_Val")
    humidity = Random_Locations.get_value(index, "Humidity")
    if (humidity != ""): #some cities don't exist in openweather -sadface-
        plt.scatter(lat_val, humidity, edgecolor="white", linewidths=1, marker="o",alpha=0.8, label="Cities")

now = datetime.datetime.now()
month = now.strftime("%b")
plt.title(f"City Latitude vs. Humidity % {now.day} {month} {now.year}")
plt.ylabel("Humidity %")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim(-100,100)
plt.ylim(0,110)

plt.savefig("LathumidPlt.png")
plt.show()

### its not blue like in the example but I like this better.
```


![png](output_8_0.png)



```python
#lat vs cloudiness plot
for index, row in Random_Locations.iterrows():
    lat_val = Random_Locations.get_value(index, "Lat_Val")
    cloud = Random_Locations.get_value(index, "Cloudiness")
    if (cloud != ""): #some cities don't exist in openweather -sadface-
        plt.scatter(lat_val, cloud, edgecolor="white", linewidths=1, marker="o",alpha=0.8, label="Cities")

now = datetime.datetime.now()
month = now.strftime("%b")
plt.title(f"City Latitude vs. Cloudiness {now.day} {month} {now.year}")
plt.ylabel("Cloudiness")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim(-100,100)
plt.ylim(-10,110)

plt.savefig("LatcloudPlt.png")
plt.show()

### its not blue like in the example but I like this better
```


![png](output_9_0.png)



```python
#lat vs windspeed plot
for index, row in Random_Locations.iterrows():
    lat_val = Random_Locations.get_value(index, "Lat_Val")
    wind = Random_Locations.get_value(index, "Wind Speed")
    if (wind != ""): #some cities don't exist in openweather -sadface-
        plt.scatter(lat_val, wind, edgecolor="white", linewidths=1, marker="o",alpha=0.8, label="Cities")

now = datetime.datetime.now()
month = now.strftime("%b")
plt.title(f"City Latitude vs. Wind Speed {now.day} {month} {now.year}")
plt.ylabel("Wind Speed")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim(-100,100)
plt.ylim(-10,60)

plt.savefig("LatwindPlt.png")
plt.show()

### its not blue like in the example but I like this better
```


![png](output_10_0.png)

