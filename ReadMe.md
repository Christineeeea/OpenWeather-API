
## WeatherPy

Trend: 
1. The closer to equator, the higher the temperature. 
2. Humidity and cloudiness seem to have no relationships with the distance from the equator. 
3. It seems like equator has 



```python
import json
import requests as req
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np
import openweathermapy.core as ow
from citipy import citipy
```


```python
apikey='25bc90a1196e6f153eece0bc0b0fc9eb'
url='http://api.openweathermap.org/data/2.5/weather?'
units='Imperial'
settings = {"units": "Imperial", "appid": apikey}
```


```python
lat_coord = np.random.uniform(-90,91,650)
lng_coord = np.random.uniform(-180,181,650)
```


```python
city_df=pd.DataFrame()
city_df['City']=''
city_df['Country Code']=''
city_df['Lat']=''
city_df['Lng']=''
city_df['Max Temp(F)']=''
city_df['Humidity(%)']=''
city_df['Cloudiness (%)']=''
city_df['Wind Speed (mph)']=''
```


```python
for i in range(len(lat_coord)):
    city_df.set_value(i,'City',citipy.nearest_city(lat_coord[i], lng_coord[i]).city_name.upper())
    city_df.set_value(i,'Country Code',citipy.nearest_city(lat_coord[i],lng_coord[i]).country_code.upper())
    city_df.set_value(i,'Lat',round(lat_coord[i],2))
    city_df.set_value(i,'Lng',round(lng_coord[i],2))
```


```python
city_df.head()
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
      <th>Country Code</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp(F)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBANY</td>
      <td>AU</td>
      <td>-84.35</td>
      <td>103.61</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>KYTLYM</td>
      <td>RU</td>
      <td>60.18</td>
      <td>58.57</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>YELLOWKNIFE</td>
      <td>CA</td>
      <td>86.24</td>
      <td>-114.29</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BREDASDORP</td>
      <td>ZA</td>
      <td>-57.12</td>
      <td>17.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SAN PATRICIO</td>
      <td>MX</td>
      <td>6.3</td>
      <td>-110.54</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
counter=1

for index, row in city_df.iterrows():
    try:
        weather_data = ow.get_current("{},{}".format(row['City'],row['Country Code']),**settings)

        city_df.set_value(index, 'Max Temp(F)', weather_data('main.temp_max'))
        city_df.set_value(index, 'Humidity(%)', weather_data('main.humidity'))
        city_df.set_value(index, 'Cloudiness (%)', weather_data('clouds.all'))
        city_df.set_value(index, 'Wind Speed (mph)', weather_data('wind.speed'))
        counter=counter+1

    except:
        city_df.set_value(index, 'Max Temp(F)', np.nan)
        city_df.set_value(index, 'Humidity(%)', np.nan)
        city_df.set_value(index, 'Cloudiness (%)', np.nan)
        city_df.set_value(index, 'Wind Speed (mph)', np.nan)
        counter=counter+1
        
city_df=city_df.dropna()        
city_df.head()
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
      <th>Country Code</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp(F)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALBANY</td>
      <td>AU</td>
      <td>-84.35</td>
      <td>103.61</td>
      <td>68.36</td>
      <td>67</td>
      <td>64</td>
      <td>11.43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>YELLOWKNIFE</td>
      <td>CA</td>
      <td>86.24</td>
      <td>-114.29</td>
      <td>-0.41</td>
      <td>77</td>
      <td>90</td>
      <td>4.7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BREDASDORP</td>
      <td>ZA</td>
      <td>-57.12</td>
      <td>17.7</td>
      <td>69.8</td>
      <td>88</td>
      <td>92</td>
      <td>3.36</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SAN PATRICIO</td>
      <td>MX</td>
      <td>6.3</td>
      <td>-110.54</td>
      <td>78.8</td>
      <td>83</td>
      <td>75</td>
      <td>6.93</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BUSSELTON</td>
      <td>AU</td>
      <td>-63.07</td>
      <td>100.91</td>
      <td>75.11</td>
      <td>79</td>
      <td>44</td>
      <td>15.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
city_df.count()
```




    City                548
    Country Code        548
    Lat                 548
    Lng                 548
    Max Temp(F)         548
    Humidity(%)         548
    Cloudiness (%)      548
    Wind Speed (mph)    548
    dtype: int64




```python
city_df.to_csv("City Weather Data.csv")
```


```python
# Temperature (F) vs. Latitude
plt.figure(figsize=(8,6))
plt.scatter(city_df['Lat'], city_df['Max Temp(F)'], facecolors='blue', edgecolors='black', marker="o")
plt.title('City Latitude vs. Max Temperature')
plt.ylabel('Max Temperature(F)')
plt.xlabel('Latitude')
plt.grid(True)
sns.set()
plt.savefig('City Latitude vs. Max Temperature.png')
plt.show()
```


![png](output_10_0.png)



```python
# Humidity (%) vs. Latitude
plt.figure(figsize=(8,6))
plt.scatter(city_df['Lat'], city_df['Humidity(%)'], facecolors='blue', edgecolors='black', marker="o")
plt.title('City Latitude vs. Humidity')
plt.ylabel('Humidity(%)')
plt.xlabel('Latitude')
plt.grid(True)
sns.set()
plt.savefig('City Latitude vs. Humidity.png')
plt.show()
```


![png](output_11_0.png)



```python
# Cloudiness (%) vs. Latitude
plt.figure(figsize=(8,6))
plt.scatter(city_df['Lat'], city_df['Cloudiness (%)'], facecolors='blue', edgecolors='black', marker="o")
plt.title('City Latitude vs. Cloudiness')
plt.ylabel('Cloudiness(%)')
plt.xlabel('Latitude')
plt.grid(True)
sns.set()
plt.savefig('City Latitude vs. Cloudiness.png')
plt.show()
```


![png](output_12_0.png)



```python
# Wind Speed (mph) vs. Latitude
plt.figure(figsize=(8,6))
plt.scatter(city_df['Lat'], city_df['Wind Speed (mph)'], facecolors='blue', edgecolors='black', marker="o")
plt.title('City Latitude vs. Wind Speed')
plt.ylabel('Wind Speed (mph)')
plt.xlabel('Latitude')
plt.grid(True)
sns.set()
plt.savefig('City Latitude vs. Wind Speed.png')
plt.show()
```


![png](output_13_0.png)


