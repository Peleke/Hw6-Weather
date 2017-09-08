

```python
#This program generates a list of 500+ latitutdes and longitudes across the world, finds the cities nearest to the 
#combination of latitude and longitude, uses the weather API to fetch the weather for each of these cities, and plots graphs
# for temperature, humidity, cloudiness, and wind speed vs these cities.
```


```python
#Here are the three observable trends based on the graphs plotted for this exercise:

#1. Temperatures are warmer towards the equator, hovering close to 100F during the current time of year (Sep), and lower, 
#   closer to the poles, hovering around 50F during the current time of year.

#2. Cloudiness does not seem to follow any specific pattern (at least as of the current time of year) and seems independent of 
#   latitude. In looking at the graph it does look like a LOT of cities across the various latitudes are currently experiencing 
#   0% cloudiness.

#3. Similarly, wind speed also seems independent of the latitude (as of the current time of year), seems to hover between 
#   0 and 10 mph, more specifically between 0 and 10 mph for the most part.
```


```python
from citipy import citipy as cp
import requests as req
import seaborn 
from random import uniform #to be used to generate the list of 500 random lat/longs
import matplotlib.pyplot as plt
import pandas as pd
from datetime import datetime as dt
```


```python
#Get at least 500 unique random latitudes and longitudes across the world. There's 4 loops, 1 for each quadrant to get an even distribution 
#across the globe.
#Fetching about 4000 random lat/longs since there are loads of duplicates on the nearest city corresponding to these lat/longs

#First initialize the list of lat, long coordinates
latLongList = []
cityList = []

#First quadrant
for i in range(0,1000):
    lat,long = uniform(-180,-90), uniform(-90, 90)
    latLong = (lat,long)
    latLongList.append(latLong)
    
    #Get the nearest city corresponding to these coordinates
    city = cp.nearest_city(lat, long)
    countryCity = (city.country_code,city.city_name)
    cityList.append(countryCity)

#Second quadrant
for i in range(1000,2001):
    lat,long = uniform(-90,0), uniform(-90, 90)
    latLong = (lat,long)
    latLongList.append(latLong)
    
    #Get the nearest city corresponding to these coordinates
    city = cp.nearest_city(lat, long)
    countryCity = (city.country_code,city.city_name)
    cityList.append(countryCity)   
    
for i in range(2001,3001):
    lat,long = uniform(0,90), uniform(-90, 90)
    latLong = (lat,long)
    latLongList.append(latLong)
    
    #Get the nearest city corresponding to these coordinates
    city = cp.nearest_city(lat, long)
    countryCity = (city.country_code,city.city_name)
    cityList.append(countryCity)   
    
for i in range(3001,4001):
    lat,long =  uniform(90,180), uniform(-90, 90)
    latLong = (lat,long)
    latLongList.append(latLong)
    
    #Get the nearest city corresponding to these coordinates
    city = cp.nearest_city(lat, long)
    countryCity = (city.country_code,city.city_name)
    cityList.append(countryCity)     
    
#Removing duplicate cities from the list

cityListUnique = set(cityList)

```


```python
#Initilaize the dataframe that will hold the weather data for the various cities
cityWeatherList=[]

#OpenWeatherMap api key
#apiKey = "eb65d0b4cd003e226c7ffd1efcc9c28d"
apiKey = "7657f21bcf60c675753623ad2c65dd0f"

#counter to track the city and country being processed
counter = 0

#Iterate through the list of 500+ unique cities and fetch the weather data from the OpenWeatherMap API
for x in cityListUnique:
    
    #Increment the counter
    counter = counter + 1
    
    #Build OpenWeatherMap url for city
    url = "http://api.openweathermap.org/data/2.5/weather?q=%s,%s&APPID=%s" % (x[1], x[0], apiKey)
    
    #Hit the OpenWeatherMap API and get weather data for the city
    weather = req.get(url).json()
    
    try:
        print("Record " + str(counter) + " | Getting weather for City:" + x[0] + ", " + "City Id:" + str(weather["id"]) + ", Country:" + x[1])
        print("url =" + url)
        
        #Get the required data from the openweathermap api json and put in a dictionary
        country = x[0]
        city = x[1]
        cityLat = weather["coord"]["lat"]
        cityTemp = (weather["main"]["temp_max"]  * 9/5) - 459.67
        cityWind = weather["wind"]["speed"]
        cityHumidity = weather["main"]["humidity"]
        cityCloudiness = weather["clouds"]["all"]
        cityWeatherDict = {"city":city,"country":country,"lat":cityLat,"temp":cityTemp,"wind":cityWind,"humidity":cityHumidity,"cloudiness":cityCloudiness}
       
        #Append dictionary to a list
        cityWeatherList.append(cityWeatherDict)
       
    except:
        print("Did not find weather for City:" + x[0]+ ", Country:" + x[1])

#Convert list of dictionaries to a dataframe -- makes it easier to pass the required columns to the scatterplot
cityWeatherdf = pd.DataFrame(cityWeatherList) 
cityWeatherdf.to_csv("CityWeather_Results.csv")
print(cityWeatherdf)
```

    Record 1 | Getting weather for City:ma, City Id:2527087, Country:tiznit
    url =http://api.openweathermap.org/data/2.5/weather?q=tiznit,ma&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 2 | Getting weather for City:af, City Id:1140026, Country:herat
    url =http://api.openweathermap.org/data/2.5/weather?q=herat,af&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 3 | Getting weather for City:is, City Id:3414079, Country:olafsvik
    url =http://api.openweathermap.org/data/2.5/weather?q=olafsvik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 4 | Getting weather for City:ml, City Id:2451478, Country:segou
    url =http://api.openweathermap.org/data/2.5/weather?q=segou,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 5 | Getting weather for City:no, City Id:779683, Country:hammerfest
    url =http://api.openweathermap.org/data/2.5/weather?q=hammerfest,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 6 | Getting weather for City:ml, City Id:2449893, Country:tessalit
    url =http://api.openweathermap.org/data/2.5/weather?q=tessalit,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 7 | Getting weather for City:bm, City Id:3573197, Country:hamilton
    url =http://api.openweathermap.org/data/2.5/weather?q=hamilton,bm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 8 | Getting weather for City:in, City Id:1258380, Country:rasra
    url =http://api.openweathermap.org/data/2.5/weather?q=rasra,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 9 | Getting weather for City:sd, City Id:376332, Country:doka
    url =http://api.openweathermap.org/data/2.5/weather?q=doka,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 10 | Getting weather for City:mu, City Id:934649, Country:cap malheureux
    url =http://api.openweathermap.org/data/2.5/weather?q=cap malheureux,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 11 | Getting weather for City:br, City Id:3471840, Country:aragarcas
    url =http://api.openweathermap.org/data/2.5/weather?q=aragarcas,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 12 | Getting weather for City:ru, City Id:529051, Country:maromitsa
    url =http://api.openweathermap.org/data/2.5/weather?q=maromitsa,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 13 | Getting weather for City:ug, City Id:230299, Country:kyenjojo
    url =http://api.openweathermap.org/data/2.5/weather?q=kyenjojo,ug&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 14 | Getting weather for City:ru, City Id:515805, Country:oktyabrskoye
    url =http://api.openweathermap.org/data/2.5/weather?q=oktyabrskoye,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 15 | Getting weather for City:fr, City Id:3032833, Country:beziers
    url =http://api.openweathermap.org/data/2.5/weather?q=beziers,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 16 | Getting weather for City:so, City Id:58933, Country:garowe
    url =http://api.openweathermap.org/data/2.5/weather?q=garowe,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 17 | Getting weather for City:iq, City Id:95788, Country:hit
    url =http://api.openweathermap.org/data/2.5/weather?q=hit,iq&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 18 | Getting weather for City:sd, City Id:363619, Country:tambura
    url =http://api.openweathermap.org/data/2.5/weather?q=tambura,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 19 | Getting weather for City:ga, City Id:2399697, Country:libreville
    url =http://api.openweathermap.org/data/2.5/weather?q=libreville,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 20 | Getting weather for City:sd, City Id:371745, Country:kutum
    url =http://api.openweathermap.org/data/2.5/weather?q=kutum,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 21 | Getting weather for City:na, City Id:3358416, Country:bethanien
    url =http://api.openweathermap.org/data/2.5/weather?q=bethanien,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 22 | Getting weather for City:uz, City Id:601349, Country:kegayli
    url =http://api.openweathermap.org/data/2.5/weather?q=kegayli,uz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 23 | Getting weather for City:fi, City Id:656130, Country:pietarsaari
    url =http://api.openweathermap.org/data/2.5/weather?q=pietarsaari,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 24 | Getting weather for City:gb, City Id:2655729, Country:bicester
    url =http://api.openweathermap.org/data/2.5/weather?q=bicester,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 25 | Getting weather for City:fr, City Id:2973355, Country:tarnos
    url =http://api.openweathermap.org/data/2.5/weather?q=tarnos,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 26 | Getting weather for City:no, City Id:777019, Country:vardo
    url =http://api.openweathermap.org/data/2.5/weather?q=vardo,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 27 | Getting weather for City:gf, City Id:3382160, Country:cayenne
    url =http://api.openweathermap.org/data/2.5/weather?q=cayenne,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 28 | Getting weather for City:es, City Id:3109642, Country:santiago de compostela
    url =http://api.openweathermap.org/data/2.5/weather?q=santiago de compostela,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 29 | Getting weather for City:ru, City Id:512247, Country:pavino
    url =http://api.openweathermap.org/data/2.5/weather?q=pavino,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 30 | Getting weather for City:br, City Id:3405215, Country:bonfim
    url =http://api.openweathermap.org/data/2.5/weather?q=bonfim,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 31 | Getting weather for City:br, City Id:3665202, Country:aripuana
    url =http://api.openweathermap.org/data/2.5/weather?q=aripuana,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 32 | Getting weather for City:pt, City Id:2268563, Country:ericeira
    url =http://api.openweathermap.org/data/2.5/weather?q=ericeira,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 33 | Getting weather for City:ar, City Id:3860443, Country:comodoro rivadavia
    url =http://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 34 | Getting weather for City:br, City Id:3470583, Country:barreiras
    url =http://api.openweathermap.org/data/2.5/weather?q=barreiras,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 35 | Getting weather for City:fi, City Id:632371, Country:varkaus
    url =http://api.openweathermap.org/data/2.5/weather?q=varkaus,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 36 | Getting weather for City:cu, City Id:3547930, Country:mantua
    url =http://api.openweathermap.org/data/2.5/weather?q=mantua,cu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 37 | Getting weather for City:co, City Id:3680090, Country:jurado
    url =http://api.openweathermap.org/data/2.5/weather?q=jurado,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 38 | Getting weather for City:mg, City Id:1083724, Country:ambanja
    url =http://api.openweathermap.org/data/2.5/weather?q=ambanja,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 39 | Getting weather for City:so, City Id:57000, Country:hobyo
    url =http://api.openweathermap.org/data/2.5/weather?q=hobyo,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 40 | Getting weather for City:za, City Id:965528, Country:phalaborwa
    url =http://api.openweathermap.org/data/2.5/weather?q=phalaborwa,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 41 | Getting weather for City:ly, City Id:86049, Country:jalu
    url =http://api.openweathermap.org/data/2.5/weather?q=jalu,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 42 | Getting weather for City:gl, City Id:3423146, Country:ilulissat
    url =http://api.openweathermap.org/data/2.5/weather?q=ilulissat,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 43 | Getting weather for City:pt, City Id:2269594, Country:cascais
    url =http://api.openweathermap.org/data/2.5/weather?q=cascais,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 44 | Getting weather for City:ca, City Id:6137462, Country:saint-augustin
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-augustin,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 45 | Getting weather for City:us, City Id:4992232, Country:escanaba
    url =http://api.openweathermap.org/data/2.5/weather?q=escanaba,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 46 | Getting weather for City:mz, City Id:1039536, Country:maxixe
    url =http://api.openweathermap.org/data/2.5/weather?q=maxixe,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 47 | Getting weather for City:gr, City Id:259265, Country:koroni
    url =http://api.openweathermap.org/data/2.5/weather?q=koroni,gr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 48 | Getting weather for City:ru, City Id:483379, Country:tumannyy
    url =http://api.openweathermap.org/data/2.5/weather?q=tumannyy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 49 | Getting weather for City:br, City Id:3665315, Country:anori
    url =http://api.openweathermap.org/data/2.5/weather?q=anori,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 50 | Getting weather for City:ru, City Id:556268, Country:ostrovnoy
    url =http://api.openweathermap.org/data/2.5/weather?q=ostrovnoy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 51 | Getting weather for City:br, City Id:3467512, Country:cangucu
    url =http://api.openweathermap.org/data/2.5/weather?q=cangucu,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 52 | Getting weather for City:ar, City Id:3846864, Country:lincoln
    url =http://api.openweathermap.org/data/2.5/weather?q=lincoln,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 53 | Getting weather for City:ru, City Id:496358, Country:severnyy
    url =http://api.openweathermap.org/data/2.5/weather?q=severnyy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 54 | Getting weather for City:br, City Id:3395981, Country:maceio
    url =http://api.openweathermap.org/data/2.5/weather?q=maceio,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 55 | Getting weather for City:kw, City Id:285663, Country:bayan
    url =http://api.openweathermap.org/data/2.5/weather?q=bayan,kw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 56 | Getting weather for City:gl, City Id:6545158, Country:illoqqortoormiut
    url =http://api.openweathermap.org/data/2.5/weather?q=illoqqortoormiut,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 57 | Getting weather for City:br, City Id:3663517, Country:manaus
    url =http://api.openweathermap.org/data/2.5/weather?q=manaus,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 58 | Getting weather for City:tz, City Id:149512, Country:tinde
    url =http://api.openweathermap.org/data/2.5/weather?q=tinde,tz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 59 | Getting weather for City:ng, City Id:2318123, Country:yenagoa
    url =http://api.openweathermap.org/data/2.5/weather?q=yenagoa,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 60 | Getting weather for City:cd, City Id:218229, Country:bongandanga
    url =http://api.openweathermap.org/data/2.5/weather?q=bongandanga,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 61 | Getting weather for City:us, City Id:5103286, Country:ringwood
    url =http://api.openweathermap.org/data/2.5/weather?q=ringwood,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 62 | Getting weather for City:ga, City Id:2397674, Country:ndjole
    url =http://api.openweathermap.org/data/2.5/weather?q=ndjole,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 63 | Getting weather for City:ar, City Id:3839982, Country:punta alta
    url =http://api.openweathermap.org/data/2.5/weather?q=punta alta,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 64 | Getting weather for City:br, City Id:3456248, Country:nortelandia
    url =http://api.openweathermap.org/data/2.5/weather?q=nortelandia,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 65 | Getting weather for City:zw, City Id:894239, Country:chegutu
    url =http://api.openweathermap.org/data/2.5/weather?q=chegutu,zw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 66 | Getting weather for City:ru, City Id:1510377, Country:belyy yar
    url =http://api.openweathermap.org/data/2.5/weather?q=belyy yar,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 67 | Getting weather for City:sl, City Id:2409914, Country:bonthe
    url =http://api.openweathermap.org/data/2.5/weather?q=bonthe,sl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 68 | Getting weather for City:ve, City Id:3625710, Country:upata
    url =http://api.openweathermap.org/data/2.5/weather?q=upata,ve&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 69 | Getting weather for City:fo, City Id:2610806, Country:vagur
    url =http://api.openweathermap.org/data/2.5/weather?q=vagur,fo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 70 | Getting weather for City:gn, City Id:2416969, Country:nzerekore
    url =http://api.openweathermap.org/data/2.5/weather?q=nzerekore,gn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 71 | Getting weather for City:sn, City Id:2244991, Country:tambacounda
    url =http://api.openweathermap.org/data/2.5/weather?q=tambacounda,sn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 72 | Getting weather for City:no, City Id:3132051, Country:vestbygda
    url =http://api.openweathermap.org/data/2.5/weather?q=vestbygda,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 73 | Getting weather for City:gb, City Id:2653775, Country:carlisle
    url =http://api.openweathermap.org/data/2.5/weather?q=carlisle,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 74 | Getting weather for City:br, City Id:3451190, Country:rio de janeiro
    url =http://api.openweathermap.org/data/2.5/weather?q=rio de janeiro,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 75 | Getting weather for City:me, City Id:3188516, Country:ulcinj
    url =http://api.openweathermap.org/data/2.5/weather?q=ulcinj,me&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 76 | Getting weather for City:ht, City Id:3723593, Country:jeremie
    url =http://api.openweathermap.org/data/2.5/weather?q=jeremie,ht&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 77 | Getting weather for City:kz, City Id:1526273, Country:emba
    url =http://api.openweathermap.org/data/2.5/weather?q=emba,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 78 | Getting weather for City:cl, City Id:3894426, Country:coihaique
    url =http://api.openweathermap.org/data/2.5/weather?q=coihaique,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 79 | Getting weather for City:ao, City Id:3347719, Country:luena
    url =http://api.openweathermap.org/data/2.5/weather?q=luena,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 80 | Getting weather for City:ag, City Id:3333198, Country:codrington
    url =http://api.openweathermap.org/data/2.5/weather?q=codrington,ag&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 81 | Getting weather for City:ru, City Id:497764, Country:selishche
    url =http://api.openweathermap.org/data/2.5/weather?q=selishche,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 82 | Getting weather for City:ir, City Id:138025, Country:damghan
    url =http://api.openweathermap.org/data/2.5/weather?q=damghan,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 83 | Getting weather for City:no, City Id:3150842, Country:jevnaker
    url =http://api.openweathermap.org/data/2.5/weather?q=jevnaker,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 84 | Getting weather for City:at, City Id:2762077, Country:waidhofen
    url =http://api.openweathermap.org/data/2.5/weather?q=waidhofen,at&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 85 | Getting weather for City:no, City Id:7626381, Country:klaebu
    url =http://api.openweathermap.org/data/2.5/weather?q=klaebu,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 86 | Getting weather for City:kw, City Id:285788, Country:doha
    url =http://api.openweathermap.org/data/2.5/weather?q=doha,kw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 87 | Getting weather for City:pe, City Id:3932145, Country:pisco
    url =http://api.openweathermap.org/data/2.5/weather?q=pisco,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 88 | Getting weather for City:us, City Id:4702828, Country:vieques
    url =http://api.openweathermap.org/data/2.5/weather?q=vieques,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 89 | Getting weather for City:ca, City Id:6067747, Country:matagami
    url =http://api.openweathermap.org/data/2.5/weather?q=matagami,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 90 | Getting weather for City:kz, City Id:1519422, Country:semey
    url =http://api.openweathermap.org/data/2.5/weather?q=semey,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 91 | Getting weather for City:ie, City Id:2963295, Country:killybegs
    url =http://api.openweathermap.org/data/2.5/weather?q=killybegs,ie&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 92 | Getting weather for City:mz, City Id:1045114, Country:inhambane
    url =http://api.openweathermap.org/data/2.5/weather?q=inhambane,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 93 | Getting weather for City:bf, City Id:2360541, Country:gayeri
    url =http://api.openweathermap.org/data/2.5/weather?q=gayeri,bf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 94 | Getting weather for City:ca, City Id:5905393, Country:bonavista
    url =http://api.openweathermap.org/data/2.5/weather?q=bonavista,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 95 | Getting weather for City:re, City Id:935255, Country:sainte-marie
    url =http://api.openweathermap.org/data/2.5/weather?q=sainte-marie,re&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 96 | Getting weather for City:ru, City Id:1489853, Country:tazovskiy
    url =http://api.openweathermap.org/data/2.5/weather?q=tazovskiy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 97 | Getting weather for City:bs, City Id:3572627, Country:cockburn town
    url =http://api.openweathermap.org/data/2.5/weather?q=cockburn town,bs&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 98 | Getting weather for City:gy, City Id:3377301, Country:mabaruma
    url =http://api.openweathermap.org/data/2.5/weather?q=mabaruma,gy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 99 | Getting weather for City:br, City Id:3663503, Country:manicore
    url =http://api.openweathermap.org/data/2.5/weather?q=manicore,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 100 | Getting weather for City:in, City Id:1253621, Country:uttukkuli
    url =http://api.openweathermap.org/data/2.5/weather?q=uttukkuli,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 101 | Getting weather for City:in, City Id:1253091, Country:vite
    url =http://api.openweathermap.org/data/2.5/weather?q=vite,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 102 | Getting weather for City:na, City Id:3356832, Country:henties bay
    url =http://api.openweathermap.org/data/2.5/weather?q=henties bay,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 103 | Getting weather for City:es, City Id:3129877, Country:aranda de duero
    url =http://api.openweathermap.org/data/2.5/weather?q=aranda de duero,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 104 | Getting weather for City:za, City Id:3366880, Country:hermanus
    url =http://api.openweathermap.org/data/2.5/weather?q=hermanus,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 105 | Getting weather for City:dz, City Id:2476660, Country:tigzirt
    url =http://api.openweathermap.org/data/2.5/weather?q=tigzirt,dz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 106 | Getting weather for City:in, City Id:1252783, Country:yarada
    url =http://api.openweathermap.org/data/2.5/weather?q=yarada,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 107 | Getting weather for City:mg, City Id:1055429, Country:toliary
    url =http://api.openweathermap.org/data/2.5/weather?q=toliary,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 108 | Getting weather for City:no, City Id:3156980, Country:floro
    url =http://api.openweathermap.org/data/2.5/weather?q=floro,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 109 | Getting weather for City:us, City Id:4984075, Country:alpena
    url =http://api.openweathermap.org/data/2.5/weather?q=alpena,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 110 | Getting weather for City:cr, City Id:3622716, Country:nicoya
    url =http://api.openweathermap.org/data/2.5/weather?q=nicoya,cr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 111 | Getting weather for City:pe, City Id:3698304, Country:chimbote
    url =http://api.openweathermap.org/data/2.5/weather?q=chimbote,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 112 | Getting weather for City:fr, City Id:2996882, Country:macon
    url =http://api.openweathermap.org/data/2.5/weather?q=macon,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 113 | Getting weather for City:br, City Id:3661944, Country:tefe
    url =http://api.openweathermap.org/data/2.5/weather?q=tefe,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 114 | Getting weather for City:is, City Id:3415667, Country:bolungarvik
    url =http://api.openweathermap.org/data/2.5/weather?q=bolungarvik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 115 | Getting weather for City:za, City Id:962367, Country:richards bay
    url =http://api.openweathermap.org/data/2.5/weather?q=richards bay,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 116 | Getting weather for City:br, City Id:3924679, Country:vilhena
    url =http://api.openweathermap.org/data/2.5/weather?q=vilhena,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 117 | Getting weather for City:br, City Id:3665016, Country:benjamin constant
    url =http://api.openweathermap.org/data/2.5/weather?q=benjamin constant,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 118 | Getting weather for City:cv, City Id:3374333, Country:praia
    url =http://api.openweathermap.org/data/2.5/weather?q=praia,cv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 119 | Getting weather for City:fi, City Id:3041733, Country:maarianhamina
    url =http://api.openweathermap.org/data/2.5/weather?q=maarianhamina,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 120 | Getting weather for City:np, City Id:1283217, Country:khandbari
    url =http://api.openweathermap.org/data/2.5/weather?q=khandbari,np&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 121 | Getting weather for City:ug, City Id:226110, Country:tororo
    url =http://api.openweathermap.org/data/2.5/weather?q=tororo,ug&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 122 | Getting weather for City:pe, City Id:3697441, Country:el alto
    url =http://api.openweathermap.org/data/2.5/weather?q=el alto,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 123 | Getting weather for City:br, City Id:3393536, Country:olinda
    url =http://api.openweathermap.org/data/2.5/weather?q=olinda,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 124 | Getting weather for City:in, City Id:1257528, Country:samdari
    url =http://api.openweathermap.org/data/2.5/weather?q=samdari,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 125 | Getting weather for City:zm, City Id:176555, Country:kawambwa
    url =http://api.openweathermap.org/data/2.5/weather?q=kawambwa,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 126 | Getting weather for City:ca, City Id:5957776, Country:fredericton
    url =http://api.openweathermap.org/data/2.5/weather?q=fredericton,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 127 | Getting weather for City:in, City Id:1278540, Country:annigeri
    url =http://api.openweathermap.org/data/2.5/weather?q=annigeri,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 128 | Getting weather for City:za, City Id:988356, Country:umzimvubu
    url =http://api.openweathermap.org/data/2.5/weather?q=umzimvubu,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 129 | Getting weather for City:br, City Id:3394023, Country:natal
    url =http://api.openweathermap.org/data/2.5/weather?q=natal,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 130 | Getting weather for City:ru, City Id:1500933, Country:labytnangi
    url =http://api.openweathermap.org/data/2.5/weather?q=labytnangi,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 131 | Getting weather for City:br, City Id:3459094, Country:laguna
    url =http://api.openweathermap.org/data/2.5/weather?q=laguna,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 132 | Getting weather for City:am, City Id:616598, Country:kamo
    url =http://api.openweathermap.org/data/2.5/weather?q=kamo,am&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 133 | Getting weather for City:us, City Id:4948924, Country:rockland
    url =http://api.openweathermap.org/data/2.5/weather?q=rockland,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 134 | Getting weather for City:bs, City Id:3572906, Country:andros town
    url =http://api.openweathermap.org/data/2.5/weather?q=andros town,bs&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 135 | Getting weather for City:in, City Id:1259184, Country:puri
    url =http://api.openweathermap.org/data/2.5/weather?q=puri,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 136 | Getting weather for City:kz, City Id:1526797, Country:alekseyevka
    url =http://api.openweathermap.org/data/2.5/weather?q=alekseyevka,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 137 | Getting weather for City:se, City Id:2720679, Country:bollnas
    url =http://api.openweathermap.org/data/2.5/weather?q=bollnas,se&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 138 | Getting weather for City:ru, City Id:570683, Country:burtunay
    url =http://api.openweathermap.org/data/2.5/weather?q=burtunay,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 139 | Getting weather for City:ly, City Id:2209055, Country:waddan
    url =http://api.openweathermap.org/data/2.5/weather?q=waddan,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 140 | Getting weather for City:ro, City Id:664281, Country:umbraresti
    url =http://api.openweathermap.org/data/2.5/weather?q=umbraresti,ro&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 141 | Getting weather for City:id, City Id:1633419, Country:padang
    url =http://api.openweathermap.org/data/2.5/weather?q=padang,id&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 142 | Getting weather for City:mu, City Id:1106643, Country:quatre cocos
    url =http://api.openweathermap.org/data/2.5/weather?q=quatre cocos,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 143 | Getting weather for City:jo, City Id:247176, Country:sabha
    url =http://api.openweathermap.org/data/2.5/weather?q=sabha,jo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 144 | Getting weather for City:ca, City Id:5989520, Country:attawapiskat
    url =http://api.openweathermap.org/data/2.5/weather?q=attawapiskat,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 145 | Getting weather for City:bb, City Id:3373652, Country:oistins
    url =http://api.openweathermap.org/data/2.5/weather?q=oistins,bb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 146 | Getting weather for City:ba, City Id:3262021, Country:crnici
    url =http://api.openweathermap.org/data/2.5/weather?q=crnici,ba&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 147 | Getting weather for City:ml, City Id:2458334, Country:douentza
    url =http://api.openweathermap.org/data/2.5/weather?q=douentza,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 148 | Getting weather for City:ru, City Id:1512019, Country:aksarka
    url =http://api.openweathermap.org/data/2.5/weather?q=aksarka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 149 | Getting weather for City:tr, City Id:298033, Country:viransehir
    url =http://api.openweathermap.org/data/2.5/weather?q=viransehir,tr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 150 | Getting weather for City:lk, City Id:1244926, Country:hambantota
    url =http://api.openweathermap.org/data/2.5/weather?q=hambantota,lk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 151 | Getting weather for City:ky, City Id:3580661, Country:george town
    url =http://api.openweathermap.org/data/2.5/weather?q=george town,ky&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 152 | Getting weather for City:ao, City Id:3346015, Country:sumbe
    url =http://api.openweathermap.org/data/2.5/weather?q=sumbe,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 153 | Getting weather for City:ca, City Id:6096551, Country:pangnirtung
    url =http://api.openweathermap.org/data/2.5/weather?q=pangnirtung,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 154 | Getting weather for City:in, City Id:1254102, Country:tulsipur
    url =http://api.openweathermap.org/data/2.5/weather?q=tulsipur,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 155 | Getting weather for City:sa, City Id:103630, Country:najran
    url =http://api.openweathermap.org/data/2.5/weather?q=najran,sa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 156 | Getting weather for City:ng, City Id:2335953, Country:jos
    url =http://api.openweathermap.org/data/2.5/weather?q=jos,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 157 | Getting weather for City:cd, City Id:210379, Country:lusambo
    url =http://api.openweathermap.org/data/2.5/weather?q=lusambo,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 158 | Getting weather for City:br, City Id:3402648, Country:carutapera
    url =http://api.openweathermap.org/data/2.5/weather?q=carutapera,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 159 | Getting weather for City:br, City Id:3664716, Country:canutama
    url =http://api.openweathermap.org/data/2.5/weather?q=canutama,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 160 | Getting weather for City:ng, City Id:2318921, Country:wukari
    url =http://api.openweathermap.org/data/2.5/weather?q=wukari,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 161 | Getting weather for City:mv, City Id:1337607, Country:kudahuvadhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=kudahuvadhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 162 | Getting weather for City:bd, City Id:1185173, Country:mathbaria
    url =http://api.openweathermap.org/data/2.5/weather?q=mathbaria,bd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 163 | Getting weather for City:gr, City Id:7668056, Country:koutsouras
    url =http://api.openweathermap.org/data/2.5/weather?q=koutsouras,gr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 164 | Getting weather for City:ca, City Id:6075562, Country:mitchell
    url =http://api.openweathermap.org/data/2.5/weather?q=mitchell,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 165 | Getting weather for City:cg, City Id:2259947, Country:gamboma
    url =http://api.openweathermap.org/data/2.5/weather?q=gamboma,cg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 166 | Getting weather for City:us, City Id:4235193, Country:carbondale
    url =http://api.openweathermap.org/data/2.5/weather?q=carbondale,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 167 | Getting weather for City:ru, City Id:1490364, Country:tabory
    url =http://api.openweathermap.org/data/2.5/weather?q=tabory,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 168 | Getting weather for City:za, City Id:964712, Country:plettenberg bay
    url =http://api.openweathermap.org/data/2.5/weather?q=plettenberg bay,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 169 | Getting weather for City:bo, City Id:3901903, Country:uyuni
    url =http://api.openweathermap.org/data/2.5/weather?q=uyuni,bo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 170 | Getting weather for City:cu, City Id:3557758, Country:guane
    url =http://api.openweathermap.org/data/2.5/weather?q=guane,cu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 171 | Getting weather for City:eg, City Id:359792, Country:aswan
    url =http://api.openweathermap.org/data/2.5/weather?q=aswan,eg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 172 | Getting weather for City:td, City Id:2427336, Country:moussoro
    url =http://api.openweathermap.org/data/2.5/weather?q=moussoro,td&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 173 | Getting weather for City:us, City Id:4791259, Country:virginia beach
    url =http://api.openweathermap.org/data/2.5/weather?q=virginia beach,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 174 | Getting weather for City:sk, City Id:3057910, Country:rajec
    url =http://api.openweathermap.org/data/2.5/weather?q=rajec,sk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 175 | Getting weather for City:mr, City Id:2247044, Country:maghama
    url =http://api.openweathermap.org/data/2.5/weather?q=maghama,mr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 176 | Getting weather for City:ga, City Id:2396518, Country:port-gentil
    url =http://api.openweathermap.org/data/2.5/weather?q=port-gentil,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 177 | Getting weather for City:br, City Id:3460785, Country:itanhem
    url =http://api.openweathermap.org/data/2.5/weather?q=itanhem,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 178 | Getting weather for City:in, City Id:1258291, Country:rayachoti
    url =http://api.openweathermap.org/data/2.5/weather?q=rayachoti,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 179 | Getting weather for City:kz, City Id:1526273, Country:astana
    url =http://api.openweathermap.org/data/2.5/weather?q=astana,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 180 | Getting weather for City:pe, City Id:3693584, Country:pimentel
    url =http://api.openweathermap.org/data/2.5/weather?q=pimentel,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 181 | Getting weather for City:fr, City Id:2998517, Country:libourne
    url =http://api.openweathermap.org/data/2.5/weather?q=libourne,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 182 | Getting weather for City:mr, City Id:2377457, Country:nouadhibou
    url =http://api.openweathermap.org/data/2.5/weather?q=nouadhibou,mr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 183 | Getting weather for City:np, City Id:1283285, Country:jumla
    url =http://api.openweathermap.org/data/2.5/weather?q=jumla,np&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 184 | Getting weather for City:ee, City Id:7522357, Country:vana-vigala
    url =http://api.openweathermap.org/data/2.5/weather?q=vana-vigala,ee&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 185 | Getting weather for City:ai, City Id:3573374, Country:the valley
    url =http://api.openweathermap.org/data/2.5/weather?q=the valley,ai&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 186 | Getting weather for City:es, City Id:3121424, Country:gijon
    url =http://api.openweathermap.org/data/2.5/weather?q=gijon,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 187 | Getting weather for City:us, City Id:4588165, Country:mount pleasant
    url =http://api.openweathermap.org/data/2.5/weather?q=mount pleasant,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 188 | Getting weather for City:dk, City Id:2611610, Country:tjaereborg
    url =http://api.openweathermap.org/data/2.5/weather?q=tjaereborg,dk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 189 | Getting weather for City:so, City Id:60019, Country:eyl
    url =http://api.openweathermap.org/data/2.5/weather?q=eyl,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 190 | Getting weather for City:pk, City Id:1168700, Country:ormara
    url =http://api.openweathermap.org/data/2.5/weather?q=ormara,pk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 191 | Getting weather for City:pe, City Id:3936456, Country:lima
    url =http://api.openweathermap.org/data/2.5/weather?q=lima,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 192 | Getting weather for City:br, City Id:3455281, Country:panambi
    url =http://api.openweathermap.org/data/2.5/weather?q=panambi,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 193 | Getting weather for City:uz, City Id:1513459, Country:krasnogorskiy
    url =http://api.openweathermap.org/data/2.5/weather?q=krasnogorskiy,uz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 194 | Getting weather for City:sl, City Id:2403698, Country:mattru
    url =http://api.openweathermap.org/data/2.5/weather?q=mattru,sl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 195 | Getting weather for City:yt, City Id:1090467, Country:boueni
    url =http://api.openweathermap.org/data/2.5/weather?q=boueni,yt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 196 | Getting weather for City:py, City Id:3867427, Country:mayor pablo lagerenza
    url =http://api.openweathermap.org/data/2.5/weather?q=mayor pablo lagerenza,py&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 197 | Getting weather for City:in, City Id:1271670, Country:gangarampur
    url =http://api.openweathermap.org/data/2.5/weather?q=gangarampur,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 198 | Getting weather for City:br, City Id:3452900, Country:porto belo
    url =http://api.openweathermap.org/data/2.5/weather?q=porto belo,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 199 | Getting weather for City:in, City Id:1264407, Country:mahbubnagar
    url =http://api.openweathermap.org/data/2.5/weather?q=mahbubnagar,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 200 | Getting weather for City:pk, City Id:1171868, Country:loralai
    url =http://api.openweathermap.org/data/2.5/weather?q=loralai,pk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 201 | Getting weather for City:in, City Id:1253237, Country:veraval
    url =http://api.openweathermap.org/data/2.5/weather?q=veraval,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 202 | Getting weather for City:ru, City Id:1489661, Country:tevriz
    url =http://api.openweathermap.org/data/2.5/weather?q=tevriz,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 203 | Getting weather for City:cn, City Id:1280037, Country:shache
    url =http://api.openweathermap.org/data/2.5/weather?q=shache,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 204 | Getting weather for City:bm, City Id:3573061, Country:saint george
    url =http://api.openweathermap.org/data/2.5/weather?q=saint george,bm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 205 | Getting weather for City:ca, City Id:6098747, Country:parry sound
    url =http://api.openweathermap.org/data/2.5/weather?q=parry sound,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 206 | Getting weather for City:ao, City Id:3347019, Country:namibe
    url =http://api.openweathermap.org/data/2.5/weather?q=namibe,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 207 | Getting weather for City:na, City Id:3354876, Country:okakarara
    url =http://api.openweathermap.org/data/2.5/weather?q=okakarara,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 208 | Getting weather for City:za, City Id:986717, Country:kruisfontein
    url =http://api.openweathermap.org/data/2.5/weather?q=kruisfontein,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 209 | Getting weather for City:is, City Id:3416888, Country:grindavik
    url =http://api.openweathermap.org/data/2.5/weather?q=grindavik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 210 | Getting weather for City:ru, City Id:1512101, Country:agirish
    url =http://api.openweathermap.org/data/2.5/weather?q=agirish,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 211 | Getting weather for City:dk, City Id:2616504, Country:nexo
    url =http://api.openweathermap.org/data/2.5/weather?q=nexo,dk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 212 | Getting weather for City:br, City Id:3471451, Country:arraial do cabo
    url =http://api.openweathermap.org/data/2.5/weather?q=arraial do cabo,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 213 | Getting weather for City:sd, City Id:372801, Country:waw
    url =http://api.openweathermap.org/data/2.5/weather?q=waw,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 214 | Getting weather for City:au, City Id:2077963, Country:albany
    url =http://api.openweathermap.org/data/2.5/weather?q=albany,au&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 215 | Getting weather for City:in, City Id:1276627, Country:bedi
    url =http://api.openweathermap.org/data/2.5/weather?q=bedi,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 216 | Getting weather for City:br, City Id:3445307, Country:vera cruz
    url =http://api.openweathermap.org/data/2.5/weather?q=vera cruz,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 217 | Getting weather for City:gl, City Id:3419842, Country:sisimiut
    url =http://api.openweathermap.org/data/2.5/weather?q=sisimiut,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 218 | Getting weather for City:ly, City Id:88319, Country:benghazi
    url =http://api.openweathermap.org/data/2.5/weather?q=benghazi,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 219 | Getting weather for City:it, City Id:2525362, Country:carbonia
    url =http://api.openweathermap.org/data/2.5/weather?q=carbonia,it&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 220 | Getting weather for City:fr, City Id:2986626, Country:plouzane
    url =http://api.openweathermap.org/data/2.5/weather?q=plouzane,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 221 | Getting weather for City:ru, City Id:1489554, Country:tisul
    url =http://api.openweathermap.org/data/2.5/weather?q=tisul,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 222 | Getting weather for City:ne, City Id:2439376, Country:tahoua
    url =http://api.openweathermap.org/data/2.5/weather?q=tahoua,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 223 | Getting weather for City:sk, City Id:3060405, Country:dolny kubin
    url =http://api.openweathermap.org/data/2.5/weather?q=dolny kubin,sk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 224 | Getting weather for City:ne, City Id:2439155, Country:tanout
    url =http://api.openweathermap.org/data/2.5/weather?q=tanout,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 225 | Getting weather for City:ca, City Id:6078372, Country:moose factory
    url =http://api.openweathermap.org/data/2.5/weather?q=moose factory,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 226 | Getting weather for City:sc, City Id:241131, Country:victoria
    url =http://api.openweathermap.org/data/2.5/weather?q=victoria,sc&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 227 | Getting weather for City:kz, City Id:1519275, Country:shar
    url =http://api.openweathermap.org/data/2.5/weather?q=shar,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 228 | Getting weather for City:br, City Id:3466165, Country:cidreira
    url =http://api.openweathermap.org/data/2.5/weather?q=cidreira,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 229 | Getting weather for City:gf, City Id:3381303, Country:kourou
    url =http://api.openweathermap.org/data/2.5/weather?q=kourou,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 230 | Getting weather for City:ci, City Id:2282006, Country:san-pedro
    url =http://api.openweathermap.org/data/2.5/weather?q=san-pedro,ci&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 231 | Getting weather for City:no, City Id:3229577, Country:aras
    url =http://api.openweathermap.org/data/2.5/weather?q=aras,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 232 | Getting weather for City:ru, City Id:486186, Country:suoyarvi
    url =http://api.openweathermap.org/data/2.5/weather?q=suoyarvi,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 233 | Getting weather for City:pt, City Id:2267226, Country:lagos
    url =http://api.openweathermap.org/data/2.5/weather?q=lagos,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 234 | Getting weather for City:mu, City Id:933995, Country:souillac
    url =http://api.openweathermap.org/data/2.5/weather?q=souillac,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 235 | Getting weather for City:ee, City Id:590939, Country:kuressaare
    url =http://api.openweathermap.org/data/2.5/weather?q=kuressaare,ee&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 236 | Getting weather for City:br, City Id:3662762, Country:porto velho
    url =http://api.openweathermap.org/data/2.5/weather?q=porto velho,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 237 | Getting weather for City:gl, City Id:3420846, Country:qaqortoq
    url =http://api.openweathermap.org/data/2.5/weather?q=qaqortoq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Did not find weather for City:sj, Country:barentsburg
    Record 239 | Getting weather for City:sv, City Id:3584003, Country:puerto el triunfo
    url =http://api.openweathermap.org/data/2.5/weather?q=puerto el triunfo,sv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 240 | Getting weather for City:co, City Id:3674292, Country:mosquera
    url =http://api.openweathermap.org/data/2.5/weather?q=mosquera,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 241 | Getting weather for City:ca, City Id:6177178, Country:wawa
    url =http://api.openweathermap.org/data/2.5/weather?q=wawa,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 242 | Getting weather for City:sh, City Id:3370903, Country:jamestown
    url =http://api.openweathermap.org/data/2.5/weather?q=jamestown,sh&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 243 | Getting weather for City:py, City Id:3867291, Country:filadelfia
    url =http://api.openweathermap.org/data/2.5/weather?q=filadelfia,py&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 244 | Getting weather for City:qa, City Id:292836, Country:abu samrah
    url =http://api.openweathermap.org/data/2.5/weather?q=abu samrah,qa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 245 | Getting weather for City:gh, City Id:2297810, Country:mumford
    url =http://api.openweathermap.org/data/2.5/weather?q=mumford,gh&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 246 | Getting weather for City:om, City Id:286621, Country:salalah
    url =http://api.openweathermap.org/data/2.5/weather?q=salalah,om&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 247 | Getting weather for City:no, City Id:777682, Country:skjervoy
    url =http://api.openweathermap.org/data/2.5/weather?q=skjervoy,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 248 | Getting weather for City:ke, City Id:189280, Country:lodwar
    url =http://api.openweathermap.org/data/2.5/weather?q=lodwar,ke&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 249 | Getting weather for City:br, City Id:3393471, Country:oriximina
    url =http://api.openweathermap.org/data/2.5/weather?q=oriximina,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 250 | Getting weather for City:ru, City Id:1507116, Country:dudinka
    url =http://api.openweathermap.org/data/2.5/weather?q=dudinka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 251 | Getting weather for City:ru, City Id:463710, Country:zemetchino
    url =http://api.openweathermap.org/data/2.5/weather?q=zemetchino,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 252 | Getting weather for City:ar, City Id:3833883, Country:trelew
    url =http://api.openweathermap.org/data/2.5/weather?q=trelew,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 253 | Getting weather for City:br, City Id:3468560, Country:cacapava do sul
    url =http://api.openweathermap.org/data/2.5/weather?q=cacapava do sul,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 254 | Getting weather for City:bb, City Id:3374083, Country:bathsheba
    url =http://api.openweathermap.org/data/2.5/weather?q=bathsheba,bb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 255 | Getting weather for City:br, City Id:3393452, Country:ouricuri
    url =http://api.openweathermap.org/data/2.5/weather?q=ouricuri,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 256 | Getting weather for City:mu, City Id:1106677, Country:bambous virieux
    url =http://api.openweathermap.org/data/2.5/weather?q=bambous virieux,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 257 | Getting weather for City:ng, City Id:2331005, Country:malumfashi
    url =http://api.openweathermap.org/data/2.5/weather?q=malumfashi,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 258 | Getting weather for City:ng, City Id:2317765, Country:zaria
    url =http://api.openweathermap.org/data/2.5/weather?q=zaria,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 259 | Getting weather for City:ar, City Id:6693229, Country:san nicolas
    url =http://api.openweathermap.org/data/2.5/weather?q=san nicolas,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 260 | Getting weather for City:ru, City Id:1497210, Country:novoagansk
    url =http://api.openweathermap.org/data/2.5/weather?q=novoagansk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 261 | Getting weather for City:hn, City Id:3607146, Country:la playona
    url =http://api.openweathermap.org/data/2.5/weather?q=la playona,hn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 262 | Getting weather for City:es, City Id:3123493, Country:ferrol
    url =http://api.openweathermap.org/data/2.5/weather?q=ferrol,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 263 | Getting weather for City:br, City Id:3665199, Country:ariquemes
    url =http://api.openweathermap.org/data/2.5/weather?q=ariquemes,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 264 | Getting weather for City:cl, City Id:3870282, Country:talcahuano
    url =http://api.openweathermap.org/data/2.5/weather?q=talcahuano,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 265 | Getting weather for City:ky, City Id:3580477, Country:west bay
    url =http://api.openweathermap.org/data/2.5/weather?q=west bay,ky&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 266 | Getting weather for City:us, City Id:4198322, Country:griffin
    url =http://api.openweathermap.org/data/2.5/weather?q=griffin,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 267 | Getting weather for City:ru, City Id:506763, Country:polyarnyy
    url =http://api.openweathermap.org/data/2.5/weather?q=polyarnyy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 268 | Getting weather for City:in, City Id:1260393, Country:paradwip
    url =http://api.openweathermap.org/data/2.5/weather?q=paradwip,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 269 | Getting weather for City:ca, City Id:6148373, Country:sioux lookout
    url =http://api.openweathermap.org/data/2.5/weather?q=sioux lookout,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 270 | Getting weather for City:mv, City Id:1337610, Country:thinadhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=thinadhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 271 | Getting weather for City:br, City Id:3388456, Country:sao jose da coroa grande
    url =http://api.openweathermap.org/data/2.5/weather?q=sao jose da coroa grande,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 272 | Getting weather for City:fr, City Id:3016956, Country:frontignan
    url =http://api.openweathermap.org/data/2.5/weather?q=frontignan,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 273 | Getting weather for City:zm, City Id:175967, Country:mporokoso
    url =http://api.openweathermap.org/data/2.5/weather?q=mporokoso,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 274 | Getting weather for City:cl, City Id:3899695, Country:ancud
    url =http://api.openweathermap.org/data/2.5/weather?q=ancud,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 275 | Getting weather for City:mz, City Id:1049261, Country:chimoio
    url =http://api.openweathermap.org/data/2.5/weather?q=chimoio,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 276 | Getting weather for City:zm, City Id:901344, Country:ndola
    url =http://api.openweathermap.org/data/2.5/weather?q=ndola,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 277 | Getting weather for City:br, City Id:3407351, Country:araioses
    url =http://api.openweathermap.org/data/2.5/weather?q=araioses,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 278 | Getting weather for City:mg, City Id:1054329, Country:vangaindrano
    url =http://api.openweathermap.org/data/2.5/weather?q=vangaindrano,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 279 | Getting weather for City:ar, City Id:3836564, Country:jujuy
    url =http://api.openweathermap.org/data/2.5/weather?q=jujuy,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 280 | Getting weather for City:pe, City Id:3696378, Country:huaraz
    url =http://api.openweathermap.org/data/2.5/weather?q=huaraz,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 281 | Getting weather for City:es, City Id:2514651, Country:los llanos de aridane
    url =http://api.openweathermap.org/data/2.5/weather?q=los llanos de aridane,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 282 | Getting weather for City:ne, City Id:2444489, Country:gaya
    url =http://api.openweathermap.org/data/2.5/weather?q=gaya,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 283 | Getting weather for City:so, City Id:64814, Country:bandarbeyla
    url =http://api.openweathermap.org/data/2.5/weather?q=bandarbeyla,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 284 | Getting weather for City:ro, City Id:679294, Country:doba
    url =http://api.openweathermap.org/data/2.5/weather?q=doba,ro&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 285 | Getting weather for City:sd, City Id:364933, Country:umm kaddadah
    url =http://api.openweathermap.org/data/2.5/weather?q=umm kaddadah,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 286 | Getting weather for City:gp, City Id:3578441, Country:saint-francois
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-francois,gp&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 287 | Getting weather for City:ga, City Id:2396853, Country:omboue
    url =http://api.openweathermap.org/data/2.5/weather?q=omboue,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 288 | Getting weather for City:ml, City Id:2460954, Country:araouane
    url =http://api.openweathermap.org/data/2.5/weather?q=araouane,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 289 | Getting weather for City:mg, City Id:1056899, Country:sambava
    url =http://api.openweathermap.org/data/2.5/weather?q=sambava,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 290 | Getting weather for City:pe, City Id:3927942, Country:tambopata
    url =http://api.openweathermap.org/data/2.5/weather?q=tambopata,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 291 | Getting weather for City:gf, City Id:3380450, Country:roura
    url =http://api.openweathermap.org/data/2.5/weather?q=roura,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 292 | Getting weather for City:it, City Id:6538896, Country:vasto
    url =http://api.openweathermap.org/data/2.5/weather?q=vasto,it&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 293 | Getting weather for City:cl, City Id:3893629, Country:coquimbo
    url =http://api.openweathermap.org/data/2.5/weather?q=coquimbo,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 294 | Getting weather for City:td, City Id:244878, Country:biltine
    url =http://api.openweathermap.org/data/2.5/weather?q=biltine,td&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 295 | Getting weather for City:mv, City Id:1337619, Country:ugoofaaru
    url =http://api.openweathermap.org/data/2.5/weather?q=ugoofaaru,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 296 | Getting weather for City:bw, City Id:933759, Country:ghanzi
    url =http://api.openweathermap.org/data/2.5/weather?q=ghanzi,bw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 297 | Getting weather for City:bf, City Id:2360372, Country:gorom-gorom
    url =http://api.openweathermap.org/data/2.5/weather?q=gorom-gorom,bf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 298 | Getting weather for City:pt, City Id:3372760, Country:praia da vitoria
    url =http://api.openweathermap.org/data/2.5/weather?q=praia da vitoria,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 299 | Getting weather for City:ng, City Id:2335727, Country:kaduna
    url =http://api.openweathermap.org/data/2.5/weather?q=kaduna,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 300 | Getting weather for City:co, City Id:3672086, Country:bajo baudo
    url =http://api.openweathermap.org/data/2.5/weather?q=bajo baudo,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 301 | Getting weather for City:mv, City Id:1337605, Country:mahibadhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=mahibadhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 302 | Getting weather for City:ru, City Id:1496511, Country:novyy urengoy
    url =http://api.openweathermap.org/data/2.5/weather?q=novyy urengoy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 303 | Getting weather for City:ru, City Id:1502422, Country:kosh-agach
    url =http://api.openweathermap.org/data/2.5/weather?q=kosh-agach,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 304 | Getting weather for City:gl, City Id:3831208, Country:qaanaaq
    url =http://api.openweathermap.org/data/2.5/weather?q=qaanaaq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 305 | Getting weather for City:ru, City Id:1497917, Country:cheuskiny
    url =http://api.openweathermap.org/data/2.5/weather?q=cheuskiny,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 306 | Getting weather for City:ru, City Id:553190, Country:kandalaksha
    url =http://api.openweathermap.org/data/2.5/weather?q=kandalaksha,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 307 | Getting weather for City:km, City Id:921889, Country:fomboni
    url =http://api.openweathermap.org/data/2.5/weather?q=fomboni,km&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 308 | Getting weather for City:lk, City Id:1235846, Country:matara
    url =http://api.openweathermap.org/data/2.5/weather?q=matara,lk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 309 | Getting weather for City:cf, City Id:237379, Country:ndele
    url =http://api.openweathermap.org/data/2.5/weather?q=ndele,cf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 310 | Getting weather for City:mv, City Id:1337606, Country:eydhafushi
    url =http://api.openweathermap.org/data/2.5/weather?q=eydhafushi,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 311 | Getting weather for City:dz, City Id:2508737, Country:aflu
    url =http://api.openweathermap.org/data/2.5/weather?q=aflu,dz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 312 | Getting weather for City:fr, City Id:3012664, Country:issoire
    url =http://api.openweathermap.org/data/2.5/weather?q=issoire,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 313 | Getting weather for City:fi, City Id:650225, Country:kuopio
    url =http://api.openweathermap.org/data/2.5/weather?q=kuopio,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 314 | Getting weather for City:pe, City Id:3694112, Country:paita
    url =http://api.openweathermap.org/data/2.5/weather?q=paita,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 315 | Getting weather for City:br, City Id:3459785, Country:joao pinheiro
    url =http://api.openweathermap.org/data/2.5/weather?q=joao pinheiro,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 316 | Getting weather for City:ir, City Id:144448, Country:ahvaz
    url =http://api.openweathermap.org/data/2.5/weather?q=ahvaz,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 317 | Getting weather for City:is, City Id:2632287, Country:dalvik
    url =http://api.openweathermap.org/data/2.5/weather?q=dalvik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 318 | Getting weather for City:sd, City Id:2762320, Country:yirol
    url =http://api.openweathermap.org/data/2.5/weather?q=yirol,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 319 | Getting weather for City:ir, City Id:115781, Country:shahr-e babak
    url =http://api.openweathermap.org/data/2.5/weather?q=shahr-e babak,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 320 | Getting weather for City:cn, City Id:3175096, Country:lasa
    url =http://api.openweathermap.org/data/2.5/weather?q=lasa,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 321 | Getting weather for City:ua, City Id:712886, Country:balta
    url =http://api.openweathermap.org/data/2.5/weather?q=balta,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 322 | Getting weather for City:gl, City Id:3421982, Country:maniitsoq
    url =http://api.openweathermap.org/data/2.5/weather?q=maniitsoq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 323 | Getting weather for City:zw, City Id:893697, Country:chinhoyi
    url =http://api.openweathermap.org/data/2.5/weather?q=chinhoyi,zw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 324 | Getting weather for City:mt, City Id:2563140, Country:dingli
    url =http://api.openweathermap.org/data/2.5/weather?q=dingli,mt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 325 | Getting weather for City:dz, City Id:643522, Country:warqla
    url =http://api.openweathermap.org/data/2.5/weather?q=warqla,dz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 326 | Getting weather for City:br, City Id:3451138, Country:rio grande
    url =http://api.openweathermap.org/data/2.5/weather?q=rio grande,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 327 | Getting weather for City:us, City Id:4497868, Country:waynesville
    url =http://api.openweathermap.org/data/2.5/weather?q=waynesville,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 328 | Getting weather for City:ng, City Id:2347209, Country:bida
    url =http://api.openweathermap.org/data/2.5/weather?q=bida,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 329 | Getting weather for City:co, City Id:3671519, Country:puerto carreno
    url =http://api.openweathermap.org/data/2.5/weather?q=puerto carreno,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 330 | Getting weather for City:dk, City Id:2613939, Country:skagen
    url =http://api.openweathermap.org/data/2.5/weather?q=skagen,dk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 331 | Getting weather for City:ru, City Id:1498402, Country:mortka
    url =http://api.openweathermap.org/data/2.5/weather?q=mortka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 332 | Getting weather for City:us, City Id:5324802, Country:arroyo
    url =http://api.openweathermap.org/data/2.5/weather?q=arroyo,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 333 | Getting weather for City:uy, City Id:3441702, Country:melo
    url =http://api.openweathermap.org/data/2.5/weather?q=melo,uy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 334 | Getting weather for City:br, City Id:3736808, Country:iranduba
    url =http://api.openweathermap.org/data/2.5/weather?q=iranduba,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 335 | Getting weather for City:bo, City Id:3905159, Country:san pedro
    url =http://api.openweathermap.org/data/2.5/weather?q=san pedro,bo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 336 | Getting weather for City:re, City Id:935215, Country:saint-philippe
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-philippe,re&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 337 | Getting weather for City:cg, City Id:2255285, Country:sibiti
    url =http://api.openweathermap.org/data/2.5/weather?q=sibiti,cg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 338 | Getting weather for City:nl, City Id:2757220, Country:den helder
    url =http://api.openweathermap.org/data/2.5/weather?q=den helder,nl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 339 | Getting weather for City:ru, City Id:1504382, Country:kargasok
    url =http://api.openweathermap.org/data/2.5/weather?q=kargasok,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 340 | Getting weather for City:co, City Id:3676623, Country:leticia
    url =http://api.openweathermap.org/data/2.5/weather?q=leticia,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 341 | Getting weather for City:pe, City Id:3939761, Country:hualmay
    url =http://api.openweathermap.org/data/2.5/weather?q=hualmay,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 342 | Getting weather for City:ne, City Id:2447513, Country:arlit
    url =http://api.openweathermap.org/data/2.5/weather?q=arlit,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 343 | Getting weather for City:us, City Id:4945819, Country:northampton
    url =http://api.openweathermap.org/data/2.5/weather?q=northampton,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 344 | Getting weather for City:za, City Id:1006984, Country:east london
    url =http://api.openweathermap.org/data/2.5/weather?q=east london,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 345 | Getting weather for City:ly, City Id:7647230, Country:gat
    url =http://api.openweathermap.org/data/2.5/weather?q=gat,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 346 | Getting weather for City:se, City Id:2685750, Country:ostersund
    url =http://api.openweathermap.org/data/2.5/weather?q=ostersund,se&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 347 | Getting weather for City:gf, City Id:3380402, Country:saint-georges
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-georges,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 348 | Getting weather for City:do, City Id:3512067, Country:bani
    url =http://api.openweathermap.org/data/2.5/weather?q=bani,do&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 349 | Getting weather for City:ca, City Id:5940934, Country:dolbeau
    url =http://api.openweathermap.org/data/2.5/weather?q=dolbeau,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 350 | Getting weather for City:gl, City Id:3421193, Country:paamiut
    url =http://api.openweathermap.org/data/2.5/weather?q=paamiut,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 351 | Getting weather for City:cd, City Id:2315417, Country:inongo
    url =http://api.openweathermap.org/data/2.5/weather?q=inongo,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 352 | Getting weather for City:br, City Id:3386213, Country:touros
    url =http://api.openweathermap.org/data/2.5/weather?q=touros,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 353 | Getting weather for City:ar, City Id:3837056, Country:san luis
    url =http://api.openweathermap.org/data/2.5/weather?q=san luis,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 354 | Getting weather for City:rw, City Id:202239, Country:gikongoro
    url =http://api.openweathermap.org/data/2.5/weather?q=gikongoro,rw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 355 | Getting weather for City:co, City Id:3671437, Country:puerto leguizamo
    url =http://api.openweathermap.org/data/2.5/weather?q=puerto leguizamo,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 356 | Getting weather for City:pe, City Id:3692073, Country:santiago de cao
    url =http://api.openweathermap.org/data/2.5/weather?q=santiago de cao,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 357 | Getting weather for City:no, City Id:3155152, Country:gravdal
    url =http://api.openweathermap.org/data/2.5/weather?q=gravdal,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 358 | Getting weather for City:tn, City Id:2469262, Country:manzil bu zalafah
    url =http://api.openweathermap.org/data/2.5/weather?q=manzil bu zalafah,tn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 359 | Getting weather for City:by, City Id:629055, Country:disna
    url =http://api.openweathermap.org/data/2.5/weather?q=disna,by&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 360 | Getting weather for City:ir, City Id:139889, Country:bukan
    url =http://api.openweathermap.org/data/2.5/weather?q=bukan,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 361 | Getting weather for City:ir, City Id:116402, Country:semnan
    url =http://api.openweathermap.org/data/2.5/weather?q=semnan,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 362 | Getting weather for City:ua, City Id:695559, Country:rohatyn
    url =http://api.openweathermap.org/data/2.5/weather?q=rohatyn,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 363 | Getting weather for City:in, City Id:1349090, Country:harindanga
    url =http://api.openweathermap.org/data/2.5/weather?q=harindanga,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 364 | Getting weather for City:np, City Id:1283467, Country:dhangadhi
    url =http://api.openweathermap.org/data/2.5/weather?q=dhangadhi,np&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 365 | Getting weather for City:ca, City Id:5924351, Country:clyde river
    url =http://api.openweathermap.org/data/2.5/weather?q=clyde river,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 366 | Getting weather for City:ca, City Id:6162654, Country:temiscaming
    url =http://api.openweathermap.org/data/2.5/weather?q=temiscaming,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 367 | Getting weather for City:fi, City Id:634096, Country:tornio
    url =http://api.openweathermap.org/data/2.5/weather?q=tornio,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 368 | Getting weather for City:ga, City Id:2396898, Country:okandja
    url =http://api.openweathermap.org/data/2.5/weather?q=okandja,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 369 | Getting weather for City:ca, City Id:6137381, Country:saint-ambroise
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-ambroise,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 370 | Getting weather for City:mu, City Id:933959, Country:triolet
    url =http://api.openweathermap.org/data/2.5/weather?q=triolet,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 371 | Getting weather for City:tr, City Id:738803, Country:terme
    url =http://api.openweathermap.org/data/2.5/weather?q=terme,tr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 372 | Getting weather for City:mg, City Id:1082243, Country:ambilobe
    url =http://api.openweathermap.org/data/2.5/weather?q=ambilobe,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 373 | Getting weather for City:bt, City Id:1252578, Country:gasa
    url =http://api.openweathermap.org/data/2.5/weather?q=gasa,bt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 374 | Getting weather for City:ec, City Id:3651949, Country:san cristobal
    url =http://api.openweathermap.org/data/2.5/weather?q=san cristobal,ec&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 375 | Getting weather for City:us, City Id:5515345, Country:winchester
    url =http://api.openweathermap.org/data/2.5/weather?q=winchester,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 376 | Getting weather for City:br, City Id:3470137, Country:belmonte
    url =http://api.openweathermap.org/data/2.5/weather?q=belmonte,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 377 | Getting weather for City:na, City Id:3354077, Country:opuwo
    url =http://api.openweathermap.org/data/2.5/weather?q=opuwo,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 378 | Getting weather for City:az, City Id:585150, Country:samur
    url =http://api.openweathermap.org/data/2.5/weather?q=samur,az&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 379 | Getting weather for City:us, City Id:4259640, Country:jasper
    url =http://api.openweathermap.org/data/2.5/weather?q=jasper,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 380 | Getting weather for City:kz, City Id:1524958, Country:boralday
    url =http://api.openweathermap.org/data/2.5/weather?q=boralday,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 381 | Getting weather for City:sa, City Id:104515, Country:mecca
    url =http://api.openweathermap.org/data/2.5/weather?q=mecca,sa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 382 | Getting weather for City:ar, City Id:3838859, Country:rio gallegos
    url =http://api.openweathermap.org/data/2.5/weather?q=rio gallegos,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 383 | Getting weather for City:pe, City Id:3938451, Country:ilave
    url =http://api.openweathermap.org/data/2.5/weather?q=ilave,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 384 | Getting weather for City:gw, City Id:2374583, Country:bubaque
    url =http://api.openweathermap.org/data/2.5/weather?q=bubaque,gw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 385 | Getting weather for City:ua, City Id:697325, Country:petrove
    url =http://api.openweathermap.org/data/2.5/weather?q=petrove,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 386 | Getting weather for City:gl, City Id:3424607, Country:tasiilaq
    url =http://api.openweathermap.org/data/2.5/weather?q=tasiilaq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 387 | Getting weather for City:ml, City Id:6759824, Country:nioro
    url =http://api.openweathermap.org/data/2.5/weather?q=nioro,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 388 | Getting weather for City:ar, City Id:3833859, Country:tres arroyos
    url =http://api.openweathermap.org/data/2.5/weather?q=tres arroyos,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 389 | Getting weather for City:mu, City Id:934322, Country:mahebourg
    url =http://api.openweathermap.org/data/2.5/weather?q=mahebourg,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 390 | Getting weather for City:zm, City Id:898188, Country:siavonga
    url =http://api.openweathermap.org/data/2.5/weather?q=siavonga,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 391 | Getting weather for City:cl, City Id:3874787, Country:punta arenas
    url =http://api.openweathermap.org/data/2.5/weather?q=punta arenas,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 392 | Getting weather for City:eg, City Id:352733, Country:marsa matruh
    url =http://api.openweathermap.org/data/2.5/weather?q=marsa matruh,eg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 393 | Getting weather for City:ru, City Id:514171, Country:ostrov
    url =http://api.openweathermap.org/data/2.5/weather?q=ostrov,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 394 | Getting weather for City:om, City Id:286245, Country:sur
    url =http://api.openweathermap.org/data/2.5/weather?q=sur,om&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 395 | Getting weather for City:us, City Id:4820938, Country:saint albans
    url =http://api.openweathermap.org/data/2.5/weather?q=saint albans,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 396 | Getting weather for City:is, City Id:3412093, Country:vestmannaeyjar
    url =http://api.openweathermap.org/data/2.5/weather?q=vestmannaeyjar,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 397 | Getting weather for City:ly, City Id:2214603, Country:marzuq
    url =http://api.openweathermap.org/data/2.5/weather?q=marzuq,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 398 | Getting weather for City:mr, City Id:2381334, Country:atar
    url =http://api.openweathermap.org/data/2.5/weather?q=atar,mr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 399 | Getting weather for City:no, City Id:780687, Country:berlevag
    url =http://api.openweathermap.org/data/2.5/weather?q=berlevag,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 400 | Getting weather for City:ie, City Id:2964782, Country:dingle
    url =http://api.openweathermap.org/data/2.5/weather?q=dingle,ie&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 401 | Getting weather for City:ru, City Id:555111, Country:ivanteyevka
    url =http://api.openweathermap.org/data/2.5/weather?q=ivanteyevka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 402 | Getting weather for City:cl, City Id:3868633, Country:vallenar
    url =http://api.openweathermap.org/data/2.5/weather?q=vallenar,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 403 | Getting weather for City:gb, City Id:2636790, Country:stornoway
    url =http://api.openweathermap.org/data/2.5/weather?q=stornoway,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 404 | Getting weather for City:fi, City Id:648090, Country:lieksa
    url =http://api.openweathermap.org/data/2.5/weather?q=lieksa,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 405 | Getting weather for City:ru, City Id:514968, Country:orgtrud
    url =http://api.openweathermap.org/data/2.5/weather?q=orgtrud,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 406 | Getting weather for City:gf, City Id:3381538, Country:grand-santi
    url =http://api.openweathermap.org/data/2.5/weather?q=grand-santi,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 407 | Getting weather for City:cg, City Id:2259655, Country:impfondo
    url =http://api.openweathermap.org/data/2.5/weather?q=impfondo,cg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 408 | Getting weather for City:za, City Id:978895, Country:margate
    url =http://api.openweathermap.org/data/2.5/weather?q=margate,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 409 | Getting weather for City:so, City Id:8045596, Country:borama
    url =http://api.openweathermap.org/data/2.5/weather?q=borama,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 410 | Getting weather for City:gl, City Id:3420768, Country:qasigiannguit
    url =http://api.openweathermap.org/data/2.5/weather?q=qasigiannguit,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 411 | Getting weather for City:za, City Id:964420, Country:port elizabeth
    url =http://api.openweathermap.org/data/2.5/weather?q=port elizabeth,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 412 | Getting weather for City:es, City Id:3113840, Country:santa eulalia del rio
    url =http://api.openweathermap.org/data/2.5/weather?q=santa eulalia del rio,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 413 | Getting weather for City:gl, City Id:3421719, Country:narsaq
    url =http://api.openweathermap.org/data/2.5/weather?q=narsaq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 414 | Getting weather for City:mg, City Id:1054500, Country:tsihombe
    url =http://api.openweathermap.org/data/2.5/weather?q=tsihombe,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 415 | Getting weather for City:ar, City Id:3429594, Country:reconquista
    url =http://api.openweathermap.org/data/2.5/weather?q=reconquista,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 416 | Getting weather for City:ng, City Id:2334802, Country:katsina
    url =http://api.openweathermap.org/data/2.5/weather?q=katsina,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 417 | Getting weather for City:tt, City Id:3573703, Country:scarborough
    url =http://api.openweathermap.org/data/2.5/weather?q=scarborough,tt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 418 | Getting weather for City:sn, City Id:2246901, Country:oussouye
    url =http://api.openweathermap.org/data/2.5/weather?q=oussouye,sn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 419 | Getting weather for City:br, City Id:3464728, Country:diamantina
    url =http://api.openweathermap.org/data/2.5/weather?q=diamantina,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 420 | Getting weather for City:ml, City Id:2450173, Country:taoudenni
    url =http://api.openweathermap.org/data/2.5/weather?q=taoudenni,ml&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 421 | Getting weather for City:in, City Id:1267390, Country:kavaratti
    url =http://api.openweathermap.org/data/2.5/weather?q=kavaratti,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 422 | Getting weather for City:gb, City Id:2654728, Country:bridlington
    url =http://api.openweathermap.org/data/2.5/weather?q=bridlington,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 423 | Getting weather for City:fr, City Id:3014867, Country:granville
    url =http://api.openweathermap.org/data/2.5/weather?q=granville,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 424 | Getting weather for City:kz, City Id:609798, Country:inderborskiy
    url =http://api.openweathermap.org/data/2.5/weather?q=inderborskiy,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 425 | Getting weather for City:ru, City Id:470905, Country:vuktyl
    url =http://api.openweathermap.org/data/2.5/weather?q=vuktyl,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 426 | Getting weather for City:ht, City Id:3728097, Country:les cayes
    url =http://api.openweathermap.org/data/2.5/weather?q=les cayes,ht&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 427 | Getting weather for City:kz, City Id:1519924, Country:kushmurun
    url =http://api.openweathermap.org/data/2.5/weather?q=kushmurun,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 428 | Getting weather for City:br, City Id:3466980, Country:caravelas
    url =http://api.openweathermap.org/data/2.5/weather?q=caravelas,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 429 | Getting weather for City:cn, City Id:1529102, Country:urumqi
    url =http://api.openweathermap.org/data/2.5/weather?q=urumqi,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 430 | Getting weather for City:bw, City Id:933815, Country:dekar
    url =http://api.openweathermap.org/data/2.5/weather?q=dekar,bw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 431 | Getting weather for City:ca, City Id:5983720, Country:iqaluit
    url =http://api.openweathermap.org/data/2.5/weather?q=iqaluit,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 432 | Getting weather for City:ca, City Id:5937073, Country:deep river
    url =http://api.openweathermap.org/data/2.5/weather?q=deep river,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 433 | Getting weather for City:ao, City Id:3351380, Country:caconda
    url =http://api.openweathermap.org/data/2.5/weather?q=caconda,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 434 | Getting weather for City:cy, City Id:146214, Country:pafos
    url =http://api.openweathermap.org/data/2.5/weather?q=pafos,cy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 435 | Getting weather for City:br, City Id:3393692, Country:itarema
    url =http://api.openweathermap.org/data/2.5/weather?q=itarema,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 436 | Getting weather for City:ru, City Id:1490266, Country:talmenka
    url =http://api.openweathermap.org/data/2.5/weather?q=talmenka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 437 | Getting weather for City:cl, City Id:3887344, Country:illapel
    url =http://api.openweathermap.org/data/2.5/weather?q=illapel,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 438 | Getting weather for City:tj, City Id:1221494, Country:karakendzha
    url =http://api.openweathermap.org/data/2.5/weather?q=karakendzha,tj&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 439 | Getting weather for City:no, City Id:6453328, Country:bangsund
    url =http://api.openweathermap.org/data/2.5/weather?q=bangsund,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 440 | Getting weather for City:ru, City Id:1501710, Country:krutaya gorka
    url =http://api.openweathermap.org/data/2.5/weather?q=krutaya gorka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 441 | Getting weather for City:in, City Id:7279741, Country:vapi
    url =http://api.openweathermap.org/data/2.5/weather?q=vapi,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 442 | Getting weather for City:sh, City Id:2411397, Country:georgetown
    url =http://api.openweathermap.org/data/2.5/weather?q=georgetown,sh&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 443 | Getting weather for City:gl, City Id:3421765, Country:nanortalik
    url =http://api.openweathermap.org/data/2.5/weather?q=nanortalik,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 444 | Getting weather for City:ru, City Id:464790, Country:zapolyarnyy
    url =http://api.openweathermap.org/data/2.5/weather?q=zapolyarnyy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 445 | Getting weather for City:ir, City Id:1161724, Country:chabahar
    url =http://api.openweathermap.org/data/2.5/weather?q=chabahar,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 446 | Getting weather for City:cf, City Id:236901, Country:ouadda
    url =http://api.openweathermap.org/data/2.5/weather?q=ouadda,cf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 447 | Getting weather for City:ar, City Id:3430863, Country:mar del plata
    url =http://api.openweathermap.org/data/2.5/weather?q=mar del plata,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 448 | Getting weather for City:kz, City Id:1518768, Country:stepnyak
    url =http://api.openweathermap.org/data/2.5/weather?q=stepnyak,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 449 | Getting weather for City:ca, City Id:6185217, Country:yarmouth
    url =http://api.openweathermap.org/data/2.5/weather?q=yarmouth,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 450 | Getting weather for City:no, City Id:779261, Country:kjollefjord
    url =http://api.openweathermap.org/data/2.5/weather?q=kjollefjord,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 451 | Getting weather for City:ng, City Id:2331447, Country:maiduguri
    url =http://api.openweathermap.org/data/2.5/weather?q=maiduguri,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 452 | Getting weather for City:ir, City Id:115770, Country:shahr-e kord
    url =http://api.openweathermap.org/data/2.5/weather?q=shahr-e kord,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 453 | Getting weather for City:ua, City Id:613273, Country:lebedyn
    url =http://api.openweathermap.org/data/2.5/weather?q=lebedyn,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 454 | Getting weather for City:ar, City Id:3833367, Country:ushuaia
    url =http://api.openweathermap.org/data/2.5/weather?q=ushuaia,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 455 | Getting weather for City:sl, City Id:2408770, Country:goderich
    url =http://api.openweathermap.org/data/2.5/weather?q=goderich,sl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 456 | Getting weather for City:ar, City Id:3839307, Country:rawson
    url =http://api.openweathermap.org/data/2.5/weather?q=rawson,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 457 | Getting weather for City:pe, City Id:3942259, Country:coracora
    url =http://api.openweathermap.org/data/2.5/weather?q=coracora,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 458 | Getting weather for City:ru, City Id:1487762, Country:belushya guba
    url =http://api.openweathermap.org/data/2.5/weather?q=belushya guba,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 459 | Getting weather for City:no, City Id:3159954, Country:bronnoysund
    url =http://api.openweathermap.org/data/2.5/weather?q=bronnoysund,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 460 | Getting weather for City:pm, City Id:3424941, Country:miquelon
    url =http://api.openweathermap.org/data/2.5/weather?q=miquelon,pm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 461 | Getting weather for City:pk, City Id:1162316, Country:yazman
    url =http://api.openweathermap.org/data/2.5/weather?q=yazman,pk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 462 | Getting weather for City:us, City Id:4355585, Country:frederick
    url =http://api.openweathermap.org/data/2.5/weather?q=frederick,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 463 | Getting weather for City:br, City Id:3665210, Country:ipixuna
    url =http://api.openweathermap.org/data/2.5/weather?q=ipixuna,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 464 | Getting weather for City:fi, City Id:653272, Country:kemijarvi
    url =http://api.openweathermap.org/data/2.5/weather?q=kemijarvi,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 465 | Getting weather for City:ly, City Id:89113, Country:ajdabiya
    url =http://api.openweathermap.org/data/2.5/weather?q=ajdabiya,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 466 | Getting weather for City:ir, City Id:1159362, Country:torbat-e jam
    url =http://api.openweathermap.org/data/2.5/weather?q=torbat-e jam,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 467 | Getting weather for City:bj, City Id:2395568, Country:aplahoue
    url =http://api.openweathermap.org/data/2.5/weather?q=aplahoue,bj&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 468 | Getting weather for City:ao, City Id:3346821, Country:ondjiva
    url =http://api.openweathermap.org/data/2.5/weather?q=ondjiva,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 469 | Getting weather for City:uy, City Id:3440777, Country:rocha
    url =http://api.openweathermap.org/data/2.5/weather?q=rocha,uy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 470 | Getting weather for City:cd, City Id:214575, Country:kampene
    url =http://api.openweathermap.org/data/2.5/weather?q=kampene,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 471 | Getting weather for City:so, City Id:55671, Country:kismayo
    url =http://api.openweathermap.org/data/2.5/weather?q=kismayo,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 472 | Getting weather for City:no, City Id:3156521, Country:holme
    url =http://api.openweathermap.org/data/2.5/weather?q=holme,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 473 | Getting weather for City:re, City Id:6690296, Country:saint-joseph
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-joseph,re&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 474 | Getting weather for City:by, City Id:619762, Country:gomel
    url =http://api.openweathermap.org/data/2.5/weather?q=gomel,by&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 475 | Getting weather for City:br, City Id:3461425, Country:ilhabela
    url =http://api.openweathermap.org/data/2.5/weather?q=ilhabela,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 476 | Getting weather for City:ru, City Id:518972, Country:novocheremshansk
    url =http://api.openweathermap.org/data/2.5/weather?q=novocheremshansk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 477 | Getting weather for City:ru, City Id:2022064, Country:kirovskiy
    url =http://api.openweathermap.org/data/2.5/weather?q=kirovskiy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 478 | Getting weather for City:za, City Id:3369157, Country:cape town
    url =http://api.openweathermap.org/data/2.5/weather?q=cape town,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 479 | Getting weather for City:ru, City Id:1511057, Country:bakchar
    url =http://api.openweathermap.org/data/2.5/weather?q=bakchar,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 480 | Getting weather for City:ru, City Id:539385, Country:kuloy
    url =http://api.openweathermap.org/data/2.5/weather?q=kuloy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 481 | Getting weather for City:cf, City Id:2386756, Country:gamboula
    url =http://api.openweathermap.org/data/2.5/weather?q=gamboula,cf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 482 | Getting weather for City:ie, City Id:2961459, Country:skibbereen
    url =http://api.openweathermap.org/data/2.5/weather?q=skibbereen,ie&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 483 | Getting weather for City:br, City Id:3386931, Country:tamandare
    url =http://api.openweathermap.org/data/2.5/weather?q=tamandare,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 484 | Getting weather for City:pe, City Id:3943957, Country:chilca
    url =http://api.openweathermap.org/data/2.5/weather?q=chilca,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 485 | Getting weather for City:cy, City Id:146286, Country:guzelyurt
    url =http://api.openweathermap.org/data/2.5/weather?q=guzelyurt,cy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 486 | Getting weather for City:is, City Id:2629833, Country:husavik
    url =http://api.openweathermap.org/data/2.5/weather?q=husavik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 487 | Getting weather for City:pt, City Id:3372472, Country:vila franca do campo
    url =http://api.openweathermap.org/data/2.5/weather?q=vila franca do campo,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 488 | Getting weather for City:ng, City Id:2346995, Country:biu
    url =http://api.openweathermap.org/data/2.5/weather?q=biu,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 489 | Getting weather for City:tm, City Id:161943, Country:gumdag
    url =http://api.openweathermap.org/data/2.5/weather?q=gumdag,tm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 490 | Getting weather for City:se, City Id:2699791, Country:koping
    url =http://api.openweathermap.org/data/2.5/weather?q=koping,se&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 491 | Getting weather for City:mu, City Id:934479, Country:grand gaube
    url =http://api.openweathermap.org/data/2.5/weather?q=grand gaube,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 492 | Getting weather for City:ga, City Id:2399001, Country:mayumba
    url =http://api.openweathermap.org/data/2.5/weather?q=mayumba,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 493 | Getting weather for City:is, City Id:3415496, Country:keflavik
    url =http://api.openweathermap.org/data/2.5/weather?q=keflavik,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 494 | Getting weather for City:cd, City Id:2312895, Country:mbandaka
    url =http://api.openweathermap.org/data/2.5/weather?q=mbandaka,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 495 | Getting weather for City:br, City Id:3396016, Country:amapa
    url =http://api.openweathermap.org/data/2.5/weather?q=amapa,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 496 | Getting weather for City:sd, City Id:377724, Country:tawkar
    url =http://api.openweathermap.org/data/2.5/weather?q=tawkar,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 497 | Getting weather for City:ru, City Id:584471, Country:abdulino
    url =http://api.openweathermap.org/data/2.5/weather?q=abdulino,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 498 | Getting weather for City:cd, City Id:213940, Country:kasongo
    url =http://api.openweathermap.org/data/2.5/weather?q=kasongo,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 499 | Getting weather for City:cv, City Id:3374210, Country:sao filipe
    url =http://api.openweathermap.org/data/2.5/weather?q=sao filipe,cv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 500 | Getting weather for City:co, City Id:3689718, Country:arauca
    url =http://api.openweathermap.org/data/2.5/weather?q=arauca,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 501 | Getting weather for City:ao, City Id:2236967, Country:soyo
    url =http://api.openweathermap.org/data/2.5/weather?q=soyo,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 502 | Getting weather for City:ne, City Id:2446796, Country:bilma
    url =http://api.openweathermap.org/data/2.5/weather?q=bilma,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 503 | Getting weather for City:ru, City Id:544084, Country:koslan
    url =http://api.openweathermap.org/data/2.5/weather?q=koslan,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 504 | Getting weather for City:ru, City Id:1488288, Country:uspenka
    url =http://api.openweathermap.org/data/2.5/weather?q=uspenka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 505 | Getting weather for City:et, City Id:336454, Country:ginir
    url =http://api.openweathermap.org/data/2.5/weather?q=ginir,et&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 506 | Getting weather for City:lk, City Id:1242110, Country:kalmunai
    url =http://api.openweathermap.org/data/2.5/weather?q=kalmunai,lk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 507 | Getting weather for City:mz, City Id:1024312, Country:mocuba
    url =http://api.openweathermap.org/data/2.5/weather?q=mocuba,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 508 | Getting weather for City:ru, City Id:1500997, Country:kytlym
    url =http://api.openweathermap.org/data/2.5/weather?q=kytlym,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 509 | Getting weather for City:kz, City Id:610298, Country:beyneu
    url =http://api.openweathermap.org/data/2.5/weather?q=beyneu,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 510 | Getting weather for City:cv, City Id:3374218, Country:santa maria
    url =http://api.openweathermap.org/data/2.5/weather?q=santa maria,cv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 511 | Getting weather for City:de, City Id:2856944, Country:oschatz
    url =http://api.openweathermap.org/data/2.5/weather?q=oschatz,de&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 512 | Getting weather for City:sd, City Id:367544, Country:sawakin
    url =http://api.openweathermap.org/data/2.5/weather?q=sawakin,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 513 | Getting weather for City:eg, City Id:358619, Country:port said
    url =http://api.openweathermap.org/data/2.5/weather?q=port said,eg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 514 | Getting weather for City:za, City Id:964406, Country:port shepstone
    url =http://api.openweathermap.org/data/2.5/weather?q=port shepstone,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 515 | Getting weather for City:in, City Id:1276782, Country:basavana bagevadi
    url =http://api.openweathermap.org/data/2.5/weather?q=basavana bagevadi,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 516 | Getting weather for City:in, City Id:1259385, Country:port blair
    url =http://api.openweathermap.org/data/2.5/weather?q=port blair,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 517 | Getting weather for City:gb, City Id:2640006, Country:portree
    url =http://api.openweathermap.org/data/2.5/weather?q=portree,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 518 | Getting weather for City:uy, City Id:3440781, Country:rivera
    url =http://api.openweathermap.org/data/2.5/weather?q=rivera,uy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 519 | Getting weather for City:ua, City Id:699311, Country:novopskov
    url =http://api.openweathermap.org/data/2.5/weather?q=novopskov,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 520 | Getting weather for City:in, City Id:1274848, Country:chamba
    url =http://api.openweathermap.org/data/2.5/weather?q=chamba,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 521 | Getting weather for City:us, City Id:4165565, Country:naples
    url =http://api.openweathermap.org/data/2.5/weather?q=naples,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 522 | Getting weather for City:mv, City Id:1282256, Country:hithadhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=hithadhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 523 | Getting weather for City:no, City Id:778707, Country:mehamn
    url =http://api.openweathermap.org/data/2.5/weather?q=mehamn,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 524 | Getting weather for City:in, City Id:1275921, Country:bhinga
    url =http://api.openweathermap.org/data/2.5/weather?q=bhinga,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 525 | Getting weather for City:pa, City Id:3709569, Country:burica
    url =http://api.openweathermap.org/data/2.5/weather?q=burica,pa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 526 | Getting weather for City:fr, City Id:2975588, Country:saverne
    url =http://api.openweathermap.org/data/2.5/weather?q=saverne,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 527 | Getting weather for City:fi, City Id:659169, Country:hamina
    url =http://api.openweathermap.org/data/2.5/weather?q=hamina,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 528 | Getting weather for City:co, City Id:3690572, Country:acandi
    url =http://api.openweathermap.org/data/2.5/weather?q=acandi,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 529 | Getting weather for City:sk, City Id:3057602, Country:sladkovicovo
    url =http://api.openweathermap.org/data/2.5/weather?q=sladkovicovo,sk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 530 | Getting weather for City:so, City Id:53654, Country:mogadishu
    url =http://api.openweathermap.org/data/2.5/weather?q=mogadishu,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 531 | Getting weather for City:bw, City Id:945945, Country:bokspits
    url =http://api.openweathermap.org/data/2.5/weather?q=bokspits,bw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 532 | Getting weather for City:es, City Id:2516860, Country:guia de isora
    url =http://api.openweathermap.org/data/2.5/weather?q=guia de isora,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 533 | Getting weather for City:ng, City Id:2322911, Country:sokoto
    url =http://api.openweathermap.org/data/2.5/weather?q=sokoto,ng&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 534 | Getting weather for City:br, City Id:3663693, Country:jutai
    url =http://api.openweathermap.org/data/2.5/weather?q=jutai,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 535 | Getting weather for City:sd, City Id:5770156, Country:uwayl
    url =http://api.openweathermap.org/data/2.5/weather?q=uwayl,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 536 | Getting weather for City:ua, City Id:693809, Country:katsiveli
    url =http://api.openweathermap.org/data/2.5/weather?q=katsiveli,ua&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 537 | Getting weather for City:kz, City Id:1526041, Country:atasu
    url =http://api.openweathermap.org/data/2.5/weather?q=atasu,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 538 | Getting weather for City:mz, City Id:1037125, Country:montepuez
    url =http://api.openweathermap.org/data/2.5/weather?q=montepuez,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 539 | Getting weather for City:fi, City Id:652977, Country:keuruu
    url =http://api.openweathermap.org/data/2.5/weather?q=keuruu,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 540 | Getting weather for City:cn, City Id:1529195, Country:shihezi
    url =http://api.openweathermap.org/data/2.5/weather?q=shihezi,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 541 | Getting weather for City:pt, City Id:3372783, Country:ponta delgada
    url =http://api.openweathermap.org/data/2.5/weather?q=ponta delgada,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 542 | Getting weather for City:br, City Id:3404558, Country:cabedelo
    url =http://api.openweathermap.org/data/2.5/weather?q=cabedelo,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 543 | Getting weather for City:ru, City Id:1495626, Country:pangody
    url =http://api.openweathermap.org/data/2.5/weather?q=pangody,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 544 | Getting weather for City:mg, City Id:1078317, Country:taolanaro
    url =http://api.openweathermap.org/data/2.5/weather?q=taolanaro,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 545 | Getting weather for City:ga, City Id:2399371, Country:makokou
    url =http://api.openweathermap.org/data/2.5/weather?q=makokou,ga&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 546 | Getting weather for City:pm, City Id:3424934, Country:saint-pierre
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-pierre,pm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 547 | Getting weather for City:in, City Id:1266838, Country:khatra
    url =http://api.openweathermap.org/data/2.5/weather?q=khatra,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 548 | Getting weather for City:ca, City Id:6324729, Country:halifax
    url =http://api.openweathermap.org/data/2.5/weather?q=halifax,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 549 | Getting weather for City:lr, City Id:2276492, Country:harper
    url =http://api.openweathermap.org/data/2.5/weather?q=harper,lr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 550 | Getting weather for City:ne, City Id:2445704, Country:diffa
    url =http://api.openweathermap.org/data/2.5/weather?q=diffa,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 551 | Getting weather for City:no, City Id:3163146, Country:andenes
    url =http://api.openweathermap.org/data/2.5/weather?q=andenes,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 552 | Getting weather for City:mu, City Id:934475, Country:grand river south east
    url =http://api.openweathermap.org/data/2.5/weather?q=grand river south east,mu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 553 | Getting weather for City:cd, City Id:218680, Country:boende
    url =http://api.openweathermap.org/data/2.5/weather?q=boende,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 554 | Getting weather for City:mg, City Id:1068971, Country:antsohihy
    url =http://api.openweathermap.org/data/2.5/weather?q=antsohihy,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 555 | Getting weather for City:no, City Id:3137469, Country:sorland
    url =http://api.openweathermap.org/data/2.5/weather?q=sorland,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 556 | Getting weather for City:in, City Id:1277273, Country:banki
    url =http://api.openweathermap.org/data/2.5/weather?q=banki,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 557 | Getting weather for City:so, City Id:50672, Country:wanlaweyn
    url =http://api.openweathermap.org/data/2.5/weather?q=wanlaweyn,so&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 558 | Getting weather for City:ru, City Id:543737, Country:kotelnich
    url =http://api.openweathermap.org/data/2.5/weather?q=kotelnich,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 559 | Getting weather for City:ug, City Id:234578, Country:bugembe
    url =http://api.openweathermap.org/data/2.5/weather?q=bugembe,ug&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 560 | Getting weather for City:ca, City Id:6155302, Country:saint anthony
    url =http://api.openweathermap.org/data/2.5/weather?q=saint anthony,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 561 | Getting weather for City:ru, City Id:571155, Country:buinsk
    url =http://api.openweathermap.org/data/2.5/weather?q=buinsk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 562 | Getting weather for City:es, City Id:2512990, Country:palma del rio
    url =http://api.openweathermap.org/data/2.5/weather?q=palma del rio,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 563 | Getting weather for City:pk, City Id:1181022, Country:chor
    url =http://api.openweathermap.org/data/2.5/weather?q=chor,pk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 564 | Getting weather for City:sa, City Id:108410, Country:riyadh
    url =http://api.openweathermap.org/data/2.5/weather?q=riyadh,sa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 565 | Getting weather for City:cd, City Id:216449, Country:gandajika
    url =http://api.openweathermap.org/data/2.5/weather?q=gandajika,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 566 | Getting weather for City:br, City Id:3664659, Country:carauari
    url =http://api.openweathermap.org/data/2.5/weather?q=carauari,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 567 | Getting weather for City:ir, City Id:124291, Country:mehran
    url =http://api.openweathermap.org/data/2.5/weather?q=mehran,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 568 | Getting weather for City:pe, City Id:3695000, Country:mancora
    url =http://api.openweathermap.org/data/2.5/weather?q=mancora,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 569 | Getting weather for City:br, City Id:3664956, Country:boca do acre
    url =http://api.openweathermap.org/data/2.5/weather?q=boca do acre,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 570 | Getting weather for City:ru, City Id:543737, Country:karaul
    url =http://api.openweathermap.org/data/2.5/weather?q=karaul,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 571 | Getting weather for City:pt, City Id:3372707, Country:ribeira grande
    url =http://api.openweathermap.org/data/2.5/weather?q=ribeira grande,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 572 | Getting weather for City:za, City Id:964432, Country:port alfred
    url =http://api.openweathermap.org/data/2.5/weather?q=port alfred,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 573 | Getting weather for City:mv, City Id:1337618, Country:manadhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=manadhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 574 | Getting weather for City:gr, City Id:258175, Country:lixourion
    url =http://api.openweathermap.org/data/2.5/weather?q=lixourion,gr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 575 | Getting weather for City:br, City Id:3393115, Country:paracuru
    url =http://api.openweathermap.org/data/2.5/weather?q=paracuru,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 576 | Getting weather for City:co, City Id:3775850, Country:bosconia
    url =http://api.openweathermap.org/data/2.5/weather?q=bosconia,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 577 | Getting weather for City:us, City Id:4500920, Country:brigantine
    url =http://api.openweathermap.org/data/2.5/weather?q=brigantine,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 578 | Getting weather for City:ru, City Id:1498283, Country:mugur-aksy
    url =http://api.openweathermap.org/data/2.5/weather?q=mugur-aksy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 579 | Getting weather for City:us, City Id:5110092, Country:brewster
    url =http://api.openweathermap.org/data/2.5/weather?q=brewster,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 580 | Getting weather for City:is, City Id:3337403, Country:hvammstangi
    url =http://api.openweathermap.org/data/2.5/weather?q=hvammstangi,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 581 | Getting weather for City:in, City Id:1265752, Country:kukshi
    url =http://api.openweathermap.org/data/2.5/weather?q=kukshi,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 582 | Getting weather for City:ru, City Id:1488903, Country:turukhansk
    url =http://api.openweathermap.org/data/2.5/weather?q=turukhansk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 583 | Getting weather for City:ma, City Id:2541830, Country:asfi
    url =http://api.openweathermap.org/data/2.5/weather?q=asfi,ma&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 584 | Getting weather for City:bf, City Id:2360073, Country:hounde
    url =http://api.openweathermap.org/data/2.5/weather?q=hounde,bf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 585 | Getting weather for City:cv, City Id:3374346, Country:ponta do sol
    url =http://api.openweathermap.org/data/2.5/weather?q=ponta do sol,cv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 586 | Getting weather for City:fo, City Id:2610343, Country:vestmanna
    url =http://api.openweathermap.org/data/2.5/weather?q=vestmanna,fo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 587 | Getting weather for City:ru, City Id:1486321, Country:yar-sale
    url =http://api.openweathermap.org/data/2.5/weather?q=yar-sale,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 588 | Getting weather for City:gr, City Id:254865, Country:poros
    url =http://api.openweathermap.org/data/2.5/weather?q=poros,gr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 589 | Getting weather for City:sd, City Id:660158, Country:ler
    url =http://api.openweathermap.org/data/2.5/weather?q=ler,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 590 | Getting weather for City:bs, City Id:3571913, Country:marsh harbour
    url =http://api.openweathermap.org/data/2.5/weather?q=marsh harbour,bs&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 591 | Getting weather for City:kz, City Id:1524606, Country:derzhavinsk
    url =http://api.openweathermap.org/data/2.5/weather?q=derzhavinsk,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 592 | Getting weather for City:ru, City Id:542769, Country:krasnaya gorka
    url =http://api.openweathermap.org/data/2.5/weather?q=krasnaya gorka,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 593 | Getting weather for City:no, City Id:3158615, Country:eidsvag
    url =http://api.openweathermap.org/data/2.5/weather?q=eidsvag,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 594 | Getting weather for City:sj, City Id:2729907, Country:longyearbyen
    url =http://api.openweathermap.org/data/2.5/weather?q=longyearbyen,sj&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 595 | Getting weather for City:sd, City Id:372884, Country:kapoeta
    url =http://api.openweathermap.org/data/2.5/weather?q=kapoeta,sd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 596 | Getting weather for City:br, City Id:3458498, Country:linhares
    url =http://api.openweathermap.org/data/2.5/weather?q=linhares,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 597 | Getting weather for City:ao, City Id:2243271, Country:cabinda
    url =http://api.openweathermap.org/data/2.5/weather?q=cabinda,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 598 | Getting weather for City:cr, City Id:3623656, Country:golfito
    url =http://api.openweathermap.org/data/2.5/weather?q=golfito,cr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 599 | Getting weather for City:ru, City Id:1488429, Country:uray
    url =http://api.openweathermap.org/data/2.5/weather?q=uray,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 600 | Getting weather for City:ru, City Id:1491999, Country:shumikha
    url =http://api.openweathermap.org/data/2.5/weather?q=shumikha,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 601 | Getting weather for City:in, City Id:1267394, Country:kavali
    url =http://api.openweathermap.org/data/2.5/weather?q=kavali,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 602 | Getting weather for City:pt, City Id:3372964, Country:lagoa
    url =http://api.openweathermap.org/data/2.5/weather?q=lagoa,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 603 | Getting weather for City:kz, City Id:1519691, Country:sarkand
    url =http://api.openweathermap.org/data/2.5/weather?q=sarkand,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 604 | Getting weather for City:us, City Id:4898182, Country:kankakee
    url =http://api.openweathermap.org/data/2.5/weather?q=kankakee,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 605 | Getting weather for City:zm, City Id:899274, Country:samfya
    url =http://api.openweathermap.org/data/2.5/weather?q=samfya,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 606 | Getting weather for City:gf, City Id:3381428, Country:iracoubo
    url =http://api.openweathermap.org/data/2.5/weather?q=iracoubo,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 607 | Getting weather for City:tm, City Id:601734, Country:dasoguz
    url =http://api.openweathermap.org/data/2.5/weather?q=dasoguz,tm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 608 | Getting weather for City:au, City Id:2075265, Country:busselton
    url =http://api.openweathermap.org/data/2.5/weather?q=busselton,au&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 609 | Getting weather for City:eg, City Id:359783, Country:asyut
    url =http://api.openweathermap.org/data/2.5/weather?q=asyut,eg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 610 | Getting weather for City:cz, City Id:3061368, Country:zliv
    url =http://api.openweathermap.org/data/2.5/weather?q=zliv,cz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 611 | Getting weather for City:ru, City Id:1502697, Country:kondinskoye
    url =http://api.openweathermap.org/data/2.5/weather?q=kondinskoye,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 612 | Getting weather for City:es, City Id:2510573, Country:teguise
    url =http://api.openweathermap.org/data/2.5/weather?q=teguise,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 613 | Getting weather for City:pe, City Id:3693468, Country:pomabamba
    url =http://api.openweathermap.org/data/2.5/weather?q=pomabamba,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 614 | Getting weather for City:no, City Id:3163506, Country:solsvik
    url =http://api.openweathermap.org/data/2.5/weather?q=solsvik,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 615 | Getting weather for City:ru, City Id:1507390, Country:dikson
    url =http://api.openweathermap.org/data/2.5/weather?q=dikson,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 616 | Getting weather for City:fr, City Id:3024440, Country:cognac
    url =http://api.openweathermap.org/data/2.5/weather?q=cognac,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 617 | Getting weather for City:na, City Id:3353383, Country:rundu
    url =http://api.openweathermap.org/data/2.5/weather?q=rundu,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 618 | Getting weather for City:pl, City Id:3081419, Country:wolsztyn
    url =http://api.openweathermap.org/data/2.5/weather?q=wolsztyn,pl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 619 | Getting weather for City:zm, City Id:915471, Country:kalabo
    url =http://api.openweathermap.org/data/2.5/weather?q=kalabo,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 620 | Getting weather for City:ar, City Id:3832899, Country:viedma
    url =http://api.openweathermap.org/data/2.5/weather?q=viedma,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 621 | Getting weather for City:ru, City Id:1505965, Country:igrim
    url =http://api.openweathermap.org/data/2.5/weather?q=igrim,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 622 | Getting weather for City:kz, City Id:1526038, Country:dzhusaly
    url =http://api.openweathermap.org/data/2.5/weather?q=dzhusaly,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 623 | Getting weather for City:us, City Id:4726440, Country:san juan
    url =http://api.openweathermap.org/data/2.5/weather?q=san juan,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 624 | Getting weather for City:pt, City Id:3373348, Country:angra
    url =http://api.openweathermap.org/data/2.5/weather?q=angra,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 625 | Getting weather for City:ca, City Id:5970737, Country:longlac
    url =http://api.openweathermap.org/data/2.5/weather?q=longlac,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 626 | Getting weather for City:mz, City Id:1052373, Country:beira
    url =http://api.openweathermap.org/data/2.5/weather?q=beira,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 627 | Getting weather for City:ru, City Id:1507565, Country:dalmatovo
    url =http://api.openweathermap.org/data/2.5/weather?q=dalmatovo,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 628 | Getting weather for City:br, City Id:3448903, Country:sao joao da barra
    url =http://api.openweathermap.org/data/2.5/weather?q=sao joao da barra,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 629 | Getting weather for City:ru, City Id:1490256, Country:talnakh
    url =http://api.openweathermap.org/data/2.5/weather?q=talnakh,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 630 | Getting weather for City:gb, City Id:2644605, Country:lerwick
    url =http://api.openweathermap.org/data/2.5/weather?q=lerwick,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 631 | Getting weather for City:br, City Id:3446077, Country:tres pontas
    url =http://api.openweathermap.org/data/2.5/weather?q=tres pontas,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 632 | Getting weather for City:ru, City Id:527581, Country:mendeleyevo
    url =http://api.openweathermap.org/data/2.5/weather?q=mendeleyevo,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 633 | Getting weather for City:sk, City Id:3059101, Country:levice
    url =http://api.openweathermap.org/data/2.5/weather?q=levice,sk&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 634 | Getting weather for City:ru, City Id:552999, Country:kapustin yar
    url =http://api.openweathermap.org/data/2.5/weather?q=kapustin yar,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 635 | Getting weather for City:tm, City Id:601551, Country:akdepe
    url =http://api.openweathermap.org/data/2.5/weather?q=akdepe,tm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 636 | Getting weather for City:ci, City Id:2281120, Country:tabou
    url =http://api.openweathermap.org/data/2.5/weather?q=tabou,ci&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 637 | Getting weather for City:gf, City Id:3380290, Country:sinnamary
    url =http://api.openweathermap.org/data/2.5/weather?q=sinnamary,gf&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 638 | Getting weather for City:pl, City Id:3094303, Country:kudowa-zdroj
    url =http://api.openweathermap.org/data/2.5/weather?q=kudowa-zdroj,pl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 639 | Getting weather for City:mz, City Id:1028434, Country:quelimane
    url =http://api.openweathermap.org/data/2.5/weather?q=quelimane,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 640 | Getting weather for City:ao, City Id:3347353, Country:menongue
    url =http://api.openweathermap.org/data/2.5/weather?q=menongue,ao&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 641 | Getting weather for City:uy, City Id:3443061, Country:chuy
    url =http://api.openweathermap.org/data/2.5/weather?q=chuy,uy&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 642 | Getting weather for City:cn, City Id:1280849, Country:kashi
    url =http://api.openweathermap.org/data/2.5/weather?q=kashi,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 643 | Getting weather for City:ve, City Id:8131514, Country:villa del rosario
    url =http://api.openweathermap.org/data/2.5/weather?q=villa del rosario,ve&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 644 | Getting weather for City:br, City Id:3925075, Country:guajara-mirim
    url =http://api.openweathermap.org/data/2.5/weather?q=guajara-mirim,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 645 | Getting weather for City:in, City Id:1278869, Country:ambagarh chauki
    url =http://api.openweathermap.org/data/2.5/weather?q=ambagarh chauki,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 646 | Getting weather for City:gl, City Id:3418910, Country:upernavik
    url =http://api.openweathermap.org/data/2.5/weather?q=upernavik,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 647 | Getting weather for City:bb, City Id:3374036, Country:bridgetown
    url =http://api.openweathermap.org/data/2.5/weather?q=bridgetown,bb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 648 | Getting weather for City:ca, City Id:6165406, Country:thompson
    url =http://api.openweathermap.org/data/2.5/weather?q=thompson,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 649 | Getting weather for City:mw, City Id:924732, Country:nkhata bay
    url =http://api.openweathermap.org/data/2.5/weather?q=nkhata bay,mw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 650 | Getting weather for City:pe, City Id:3929520, Country:marcona
    url =http://api.openweathermap.org/data/2.5/weather?q=marcona,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 651 | Getting weather for City:dz, City Id:2507480, Country:algiers
    url =http://api.openweathermap.org/data/2.5/weather?q=algiers,dz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 652 | Getting weather for City:ir, City Id:1113217, Country:zabol
    url =http://api.openweathermap.org/data/2.5/weather?q=zabol,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 653 | Getting weather for City:gh, City Id:2294915, Country:takoradi
    url =http://api.openweathermap.org/data/2.5/weather?q=takoradi,gh&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 654 | Getting weather for City:gl, City Id:3422683, Country:kangaatsiaq
    url =http://api.openweathermap.org/data/2.5/weather?q=kangaatsiaq,gl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 655 | Getting weather for City:br, City Id:3395458, Country:maragogi
    url =http://api.openweathermap.org/data/2.5/weather?q=maragogi,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 656 | Getting weather for City:cl, City Id:3896218, Country:castro
    url =http://api.openweathermap.org/data/2.5/weather?q=castro,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 657 | Getting weather for City:fr, City Id:2981206, Country:saint-chamond
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-chamond,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 658 | Getting weather for City:ca, City Id:6943832, Country:riviere-au-renard
    url =http://api.openweathermap.org/data/2.5/weather?q=riviere-au-renard,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 659 | Getting weather for City:br, City Id:3407682, Country:anajatuba
    url =http://api.openweathermap.org/data/2.5/weather?q=anajatuba,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 660 | Getting weather for City:ru, City Id:524681, Country:mrakovo
    url =http://api.openweathermap.org/data/2.5/weather?q=mrakovo,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 661 | Getting weather for City:ru, City Id:511794, Country:pechora
    url =http://api.openweathermap.org/data/2.5/weather?q=pechora,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 662 | Getting weather for City:us, City Id:4146901, Country:bayonet point
    url =http://api.openweathermap.org/data/2.5/weather?q=bayonet point,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 663 | Getting weather for City:sl, City Id:2403896, Country:serabu
    url =http://api.openweathermap.org/data/2.5/weather?q=serabu,sl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 664 | Getting weather for City:mv, City Id:1337610, Country:viligili
    url =http://api.openweathermap.org/data/2.5/weather?q=viligili,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 665 | Getting weather for City:ir, City Id:122397, Country:neka
    url =http://api.openweathermap.org/data/2.5/weather?q=neka,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 666 | Getting weather for City:cl, City Id:3883457, Country:lebu
    url =http://api.openweathermap.org/data/2.5/weather?q=lebu,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 667 | Getting weather for City:ht, City Id:3723440, Country:kenscoff
    url =http://api.openweathermap.org/data/2.5/weather?q=kenscoff,ht&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 668 | Getting weather for City:fo, City Id:2618795, Country:klaksvik
    url =http://api.openweathermap.org/data/2.5/weather?q=klaksvik,fo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 669 | Getting weather for City:za, City Id:1015776, Country:bredasdorp
    url =http://api.openweathermap.org/data/2.5/weather?q=bredasdorp,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 670 | Getting weather for City:cd, City Id:204953, Country:tshikapa
    url =http://api.openweathermap.org/data/2.5/weather?q=tshikapa,cd&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 671 | Getting weather for City:ec, City Id:3655185, Country:nueva loja
    url =http://api.openweathermap.org/data/2.5/weather?q=nueva loja,ec&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 672 | Getting weather for City:ru, City Id:1503153, Country:klyuchi
    url =http://api.openweathermap.org/data/2.5/weather?q=klyuchi,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 673 | Getting weather for City:br, City Id:3472969, Country:aguai
    url =http://api.openweathermap.org/data/2.5/weather?q=aguai,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 674 | Getting weather for City:ru, City Id:1506268, Country:gornopravdinsk
    url =http://api.openweathermap.org/data/2.5/weather?q=gornopravdinsk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 675 | Getting weather for City:br, City Id:3445026, Country:vila velha
    url =http://api.openweathermap.org/data/2.5/weather?q=vila velha,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 676 | Getting weather for City:gb, City Id:2634032, Country:whitley bay
    url =http://api.openweathermap.org/data/2.5/weather?q=whitley bay,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 677 | Getting weather for City:fr, City Id:2991772, Country:morlaix
    url =http://api.openweathermap.org/data/2.5/weather?q=morlaix,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 678 | Getting weather for City:in, City Id:1257259, Country:saraipali
    url =http://api.openweathermap.org/data/2.5/weather?q=saraipali,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 679 | Getting weather for City:na, City Id:3357247, Country:gobabis
    url =http://api.openweathermap.org/data/2.5/weather?q=gobabis,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 680 | Getting weather for City:cm, City Id:2222230, Country:sangmelima
    url =http://api.openweathermap.org/data/2.5/weather?q=sangmelima,cm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 681 | Getting weather for City:rs, City Id:793111, Country:arandelovac
    url =http://api.openweathermap.org/data/2.5/weather?q=arandelovac,rs&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 682 | Getting weather for City:ru, City Id:1544819, Country:amderma
    url =http://api.openweathermap.org/data/2.5/weather?q=amderma,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 683 | Getting weather for City:in, City Id:1263649, Country:manoharpur
    url =http://api.openweathermap.org/data/2.5/weather?q=manoharpur,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 684 | Getting weather for City:is, City Id:2630299, Country:hofn
    url =http://api.openweathermap.org/data/2.5/weather?q=hofn,is&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 685 | Getting weather for City:br, City Id:3460064, Country:jatai
    url =http://api.openweathermap.org/data/2.5/weather?q=jatai,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 686 | Getting weather for City:fi, City Id:649924, Country:kuusamo
    url =http://api.openweathermap.org/data/2.5/weather?q=kuusamo,fi&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 687 | Getting weather for City:py, City Id:3437056, Country:doctor pedro p. pena
    url =http://api.openweathermap.org/data/2.5/weather?q=doctor pedro p. pena,py&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 688 | Getting weather for City:kz, City Id:1526038, Country:atbasar
    url =http://api.openweathermap.org/data/2.5/weather?q=atbasar,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 689 | Getting weather for City:mx, City Id:3521972, Country:panaba
    url =http://api.openweathermap.org/data/2.5/weather?q=panaba,mx&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 690 | Getting weather for City:ma, City Id:2542997, Country:marrakesh
    url =http://api.openweathermap.org/data/2.5/weather?q=marrakesh,ma&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 691 | Getting weather for City:no, City Id:7626370, Country:bud
    url =http://api.openweathermap.org/data/2.5/weather?q=bud,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 692 | Getting weather for City:in, City Id:1272140, Country:dwarka
    url =http://api.openweathermap.org/data/2.5/weather?q=dwarka,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 693 | Getting weather for City:ca, City Id:5919915, Country:chapleau
    url =http://api.openweathermap.org/data/2.5/weather?q=chapleau,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 694 | Getting weather for City:id, City Id:1214488, Country:meulaboh
    url =http://api.openweathermap.org/data/2.5/weather?q=meulaboh,id&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 695 | Getting weather for City:zw, City Id:884814, Country:mvuma
    url =http://api.openweathermap.org/data/2.5/weather?q=mvuma,zw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 696 | Getting weather for City:ca, City Id:5960603, Country:geraldton
    url =http://api.openweathermap.org/data/2.5/weather?q=geraldton,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 697 | Getting weather for City:no, City Id:3141667, Country:roald
    url =http://api.openweathermap.org/data/2.5/weather?q=roald,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 698 | Getting weather for City:br, City Id:3662927, Country:pauini
    url =http://api.openweathermap.org/data/2.5/weather?q=pauini,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 699 | Getting weather for City:es, City Id:3115824, Country:muros
    url =http://api.openweathermap.org/data/2.5/weather?q=muros,es&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 700 | Getting weather for City:do, City Id:3496021, Country:nagua
    url =http://api.openweathermap.org/data/2.5/weather?q=nagua,do&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 701 | Getting weather for City:mz, City Id:1024552, Country:xai-xai
    url =http://api.openweathermap.org/data/2.5/weather?q=xai-xai,mz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 702 | Getting weather for City:mv, City Id:1337612, Country:dhidhdhoo
    url =http://api.openweathermap.org/data/2.5/weather?q=dhidhdhoo,mv&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 703 | Getting weather for City:ca, City Id:5919850, Country:chapais
    url =http://api.openweathermap.org/data/2.5/weather?q=chapais,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 704 | Getting weather for City:uz, City Id:7530769, Country:karauzyak
    url =http://api.openweathermap.org/data/2.5/weather?q=karauzyak,uz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 705 | Getting weather for City:py, City Id:3438102, Country:fuerte olimpo
    url =http://api.openweathermap.org/data/2.5/weather?q=fuerte olimpo,py&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 706 | Getting weather for City:us, City Id:4985711, Country:benton harbor
    url =http://api.openweathermap.org/data/2.5/weather?q=benton harbor,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 707 | Getting weather for City:ir, City Id:141736, Country:bam
    url =http://api.openweathermap.org/data/2.5/weather?q=bam,ir&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 708 | Getting weather for City:er, City Id:343300, Country:asmara
    url =http://api.openweathermap.org/data/2.5/weather?q=asmara,er&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 709 | Getting weather for City:uz, City Id:1513990, Country:gazli
    url =http://api.openweathermap.org/data/2.5/weather?q=gazli,uz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 710 | Getting weather for City:pt, City Id:2264923, Country:peniche
    url =http://api.openweathermap.org/data/2.5/weather?q=peniche,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 711 | Getting weather for City:uz, City Id:1513087, Country:nurota
    url =http://api.openweathermap.org/data/2.5/weather?q=nurota,uz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 712 | Getting weather for City:td, City Id:2432678, Country:faya
    url =http://api.openweathermap.org/data/2.5/weather?q=faya,td&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 713 | Getting weather for City:om, City Id:287832, Country:ibra
    url =http://api.openweathermap.org/data/2.5/weather?q=ibra,om&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 714 | Getting weather for City:bo, City Id:3901547, Country:villamontes
    url =http://api.openweathermap.org/data/2.5/weather?q=villamontes,bo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 715 | Getting weather for City:et, City Id:343593, Country:areka
    url =http://api.openweathermap.org/data/2.5/weather?q=areka,et&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 716 | Getting weather for City:bo, City Id:3836873, Country:san miguel
    url =http://api.openweathermap.org/data/2.5/weather?q=san miguel,bo&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 717 | Getting weather for City:fr, City Id:2994902, Country:mazamet
    url =http://api.openweathermap.org/data/2.5/weather?q=mazamet,fr&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 718 | Getting weather for City:km, City Id:921786, Country:mitsamiouli
    url =http://api.openweathermap.org/data/2.5/weather?q=mitsamiouli,km&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 719 | Getting weather for City:sa, City Id:107304, Country:buraydah
    url =http://api.openweathermap.org/data/2.5/weather?q=buraydah,sa&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 720 | Getting weather for City:id, City Id:1649150, Country:bengkulu
    url =http://api.openweathermap.org/data/2.5/weather?q=bengkulu,id&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 721 | Getting weather for City:au, City Id:2074865, Country:carnarvon
    url =http://api.openweathermap.org/data/2.5/weather?q=carnarvon,au&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 722 | Getting weather for City:ru, City Id:1490796, Country:strezhevoy
    url =http://api.openweathermap.org/data/2.5/weather?q=strezhevoy,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 723 | Getting weather for City:pe, City Id:3945985, Country:camana
    url =http://api.openweathermap.org/data/2.5/weather?q=camana,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 724 | Getting weather for City:ly, City Id:88533, Country:awjilah
    url =http://api.openweathermap.org/data/2.5/weather?q=awjilah,ly&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 725 | Getting weather for City:br, City Id:3451051, Country:rio verde de mato grosso
    url =http://api.openweathermap.org/data/2.5/weather?q=rio verde de mato grosso,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 726 | Getting weather for City:ne, City Id:2448245, Country:abalak
    url =http://api.openweathermap.org/data/2.5/weather?q=abalak,ne&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 727 | Getting weather for City:pt, City Id:2270385, Country:camacha
    url =http://api.openweathermap.org/data/2.5/weather?q=camacha,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 728 | Getting weather for City:tj, City Id:1220826, Country:farkhor
    url =http://api.openweathermap.org/data/2.5/weather?q=farkhor,tj&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 729 | Getting weather for City:za, City Id:3361934, Country:saldanha
    url =http://api.openweathermap.org/data/2.5/weather?q=saldanha,za&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 730 | Getting weather for City:mg, City Id:1081530, Country:ambodifototra
    url =http://api.openweathermap.org/data/2.5/weather?q=ambodifototra,mg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 731 | Getting weather for City:ru, City Id:863061, Country:usinsk
    url =http://api.openweathermap.org/data/2.5/weather?q=usinsk,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 732 | Getting weather for City:ru, City Id:471607, Country:voskresenskoye
    url =http://api.openweathermap.org/data/2.5/weather?q=voskresenskoye,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 733 | Getting weather for City:zm, City Id:898947, Country:senanga
    url =http://api.openweathermap.org/data/2.5/weather?q=senanga,zm&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 734 | Getting weather for City:dz, City Id:2508813, Country:adrar
    url =http://api.openweathermap.org/data/2.5/weather?q=adrar,dz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 735 | Getting weather for City:kz, City Id:1519934, Country:karkaralinsk
    url =http://api.openweathermap.org/data/2.5/weather?q=karkaralinsk,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 736 | Getting weather for City:bw, City Id:933416, Country:manyana
    url =http://api.openweathermap.org/data/2.5/weather?q=manyana,bw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 737 | Getting weather for City:kz, City Id:610612, Country:aktau
    url =http://api.openweathermap.org/data/2.5/weather?q=aktau,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 738 | Getting weather for City:us, City Id:4360369, Country:laurel
    url =http://api.openweathermap.org/data/2.5/weather?q=laurel,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 739 | Getting weather for City:kz, City Id:1524325, Country:ekibastuz
    url =http://api.openweathermap.org/data/2.5/weather?q=ekibastuz,kz&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 740 | Getting weather for City:cl, City Id:3870243, Country:taltal
    url =http://api.openweathermap.org/data/2.5/weather?q=taltal,cl&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 741 | Getting weather for City:in, City Id:1264976, Country:leh
    url =http://api.openweathermap.org/data/2.5/weather?q=leh,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 742 | Getting weather for City:ar, City Id:3838874, Country:rio cuarto
    url =http://api.openweathermap.org/data/2.5/weather?q=rio cuarto,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 743 | Getting weather for City:gh, City Id:2299625, Country:kintampo
    url =http://api.openweathermap.org/data/2.5/weather?q=kintampo,gh&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 744 | Getting weather for City:gb, City Id:2633982, Country:wick
    url =http://api.openweathermap.org/data/2.5/weather?q=wick,gb&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 745 | Getting weather for City:br, City Id:3397763, Country:jacareacanga
    url =http://api.openweathermap.org/data/2.5/weather?q=jacareacanga,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 746 | Getting weather for City:pe, City Id:3691954, Country:sechura
    url =http://api.openweathermap.org/data/2.5/weather?q=sechura,pe&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 747 | Getting weather for City:ca, City Id:6138260, Country:saint-gabriel
    url =http://api.openweathermap.org/data/2.5/weather?q=saint-gabriel,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 748 | Getting weather for City:pt, City Id:2742765, Country:areosa
    url =http://api.openweathermap.org/data/2.5/weather?q=areosa,pt&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 749 | Getting weather for City:cu, City Id:3557332, Country:guisa
    url =http://api.openweathermap.org/data/2.5/weather?q=guisa,cu&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 750 | Getting weather for City:it, City Id:3172241, Country:noceto
    url =http://api.openweathermap.org/data/2.5/weather?q=noceto,it&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 751 | Getting weather for City:ar, City Id:3430443, Country:necochea
    url =http://api.openweathermap.org/data/2.5/weather?q=necochea,ar&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 752 | Getting weather for City:in, City Id:1252795, Country:yanam
    url =http://api.openweathermap.org/data/2.5/weather?q=yanam,in&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 753 | Getting weather for City:ca, City Id:6167817, Country:torbay
    url =http://api.openweathermap.org/data/2.5/weather?q=torbay,ca&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 754 | Getting weather for City:ru, City Id:582423, Country:alnashi
    url =http://api.openweathermap.org/data/2.5/weather?q=alnashi,ru&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 755 | Getting weather for City:co, City Id:3668605, Country:santa marta
    url =http://api.openweathermap.org/data/2.5/weather?q=santa marta,co&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 756 | Getting weather for City:na, City Id:3355672, Country:luderitz
    url =http://api.openweathermap.org/data/2.5/weather?q=luderitz,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 757 | Getting weather for City:br, City Id:3394372, Country:morros
    url =http://api.openweathermap.org/data/2.5/weather?q=morros,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 758 | Getting weather for City:tc, City Id:3576916, Country:cockburn harbour
    url =http://api.openweathermap.org/data/2.5/weather?q=cockburn harbour,tc&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 759 | Getting weather for City:us, City Id:4565031, Country:guanica
    url =http://api.openweathermap.org/data/2.5/weather?q=guanica,us&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 760 | Getting weather for City:zw, City Id:878549, Country:zvishavane
    url =http://api.openweathermap.org/data/2.5/weather?q=zvishavane,zw&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 761 | Getting weather for City:na, City Id:3354247, Country:ondangwa
    url =http://api.openweathermap.org/data/2.5/weather?q=ondangwa,na&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 762 | Getting weather for City:br, City Id:3663142, Country:novo aripuana
    url =http://api.openweathermap.org/data/2.5/weather?q=novo aripuana,br&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 763 | Getting weather for City:no, City Id:779622, Country:havoysund
    url =http://api.openweathermap.org/data/2.5/weather?q=havoysund,no&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 764 | Getting weather for City:eg, City Id:591968, Country:esna
    url =http://api.openweathermap.org/data/2.5/weather?q=esna,eg&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 765 | Getting weather for City:cn, City Id:1529376, Country:korla
    url =http://api.openweathermap.org/data/2.5/weather?q=korla,cn&APPID=7657f21bcf60c675753623ad2c65dd0f
    Record 766 | Getting weather for City:in, City Id:1263942, Country:manavalakurichi
    url =http://api.openweathermap.org/data/2.5/weather?q=manavalakurichi,in&APPID=7657f21bcf60c675753623ad2c65dd0f
                           city  cloudiness country  humidity    lat     temp  \
    0                    tiznit           0      ma        68  29.58  70.1114   
    1                     herat           0      af        38  34.35  67.6814   
    2                  olafsvik           0      is       100  64.89  49.7714   
    3                     segou           8      ml        87  13.43  79.8314   
    4                hammerfest          20      no        75  70.66  46.4000   
    5                  tessalit          20      ml        18  20.20  81.0014   
    6                  hamilton          20      bm        70  32.29  84.2000   
    7                     rasra           0      in        87  25.85  78.3914   
    8                      doka          20      sd        84  13.52  76.0514   
    9            cap malheureux          75      mu        56 -19.98  73.4000   
    10                aragarcas          44      br        22 -15.90  94.3214   
    11                maromitsa          92      ru        99  59.87  55.2614   
    12                 kyenjojo          20      ug        90   0.63  59.3114   
    13             oktyabrskoye           0      ru        86  43.06  51.9314   
    14                  beziers           0      fr        82  43.35  66.2000   
    15                   garowe          12      so        45   8.41  83.4314   
    16                      hit           0      iq        17  33.64  74.7914   
    17                  tambura          36      sd        93   4.09  66.6914   
    18               libreville          40      ga        78   0.39  78.8000   
    19                    kutum          68      sd        55  14.20  75.5114   
    20                bethanien          20      na        25 -26.48  56.8814   
    21                  kegayli           0      uz        64  42.78  69.8000   
    22              pietarsaari          90      fi        93  63.67  51.8000   
    23                 bicester          36      gb       100  51.90  57.2000   
    24                   tarnos          75      fr        77  43.54  64.4000   
    25                    vardo          75      no        75  70.37  44.6000   
    26                  cayenne           0      gf        78   4.93  82.4000   
    27   santiago de compostela          75      es       100  42.88  62.6000   
    28                   pavino         100      ru       100  59.11  52.6514   
    29                   bonfim          32      br        38   3.08  94.5914   
    ..                      ...         ...     ...       ...    ...      ...   
    735                   aktau           0      kz        93  43.65  62.6000   
    736                  laurel          75      us        40  39.10  75.2000   
    737               ekibastuz          12      kz        86  51.67  41.7614   
    738                  taltal           0      cl        70 -25.40  58.0514   
    739                     leh           8      in        70  34.17  17.6414   
    740              rio cuarto           0      ar        42 -33.13  66.2000   
    741                kintampo          76      gh        83   8.06  76.1414   
    742                    wick          20      gb        93  58.43  51.8000   
    743            jacareacanga          24      br        30  -6.22  97.5614   
    744                 sechura           0      pe        76  -5.56  72.1814   
    745           saint-gabriel          88      ca        80  46.30  59.2214   
    746                  areosa          90      pt        93  41.72  62.6000   
    747                   guisa          90      cu        94  20.25  77.0000   
    748                  noceto          40      it        68  44.81  71.6000   
    749                necochea           8      ar        63 -38.55  58.6814   
    750                   yanam          44      in        86  16.73  80.8214   
    751                  torbay          90      ca        93  47.67  64.4000   
    752                 alnashi          92      ru        98  56.19  56.1614   
    753             santa marta          75      co       100  11.24  78.8000   
    754                luderitz           0      na        87 -26.65  55.4000   
    755                  morros          80      br        29  -9.45  91.2614   
    756        cockburn harbour          36      tc        95  21.73  86.1314   
    757                 guanica          40      us        78  17.99  82.4000   
    758              zvishavane           0      zw        54 -20.33  62.4614   
    759                ondangwa           0      na        26 -17.92  68.8514   
    760           novo aripuana          44      br        63  -5.12  87.4814   
    761               havoysund          20      no        70  71.00  46.4000   
    762                    esna          56      eg        96  59.04  49.1414   
    763                   korla           0      cn        92  41.76  51.1214   
    764         manavalakurichi          40      in        88   8.13  78.8000   
    
          wind  
    0     0.77  
    1     4.47  
    2     6.57  
    3     1.22  
    4     6.70  
    5     1.07  
    6     3.10  
    7     1.52  
    8     1.27  
    9     7.20  
    10    1.62  
    11    2.22  
    12    1.22  
    13    1.02  
    14    2.10  
    15    6.82  
    16    1.97  
    17    2.32  
    18    3.10  
    19    4.02  
    20    1.42  
    21    2.10  
    22    1.50  
    23    4.60  
    24    2.60  
    25   10.30  
    26    3.10  
    27    1.50  
    28    1.67  
    29    2.97  
    ..     ...  
    735   3.00  
    736   2.60  
    737   2.12  
    738   1.52  
    739   0.72  
    740   9.80  
    741   1.47  
    742   2.10  
    743   1.12  
    744   5.52  
    745   5.27  
    746   2.60  
    747  12.30  
    748   2.10  
    749   3.92  
    750   1.92  
    751   7.70  
    752   4.47  
    753   2.10  
    754   6.70  
    755   4.07  
    756  12.12  
    757   3.10  
    758   3.32  
    759   3.42  
    760   1.32  
    761   6.70  
    762   2.77  
    763   2.72  
    764   1.00  
    
    [765 rows x 7 columns]
    


```python
#Plot scatterplot for Temperature (F) vs. Latitude
plt.title("City Latitude vs Max Temperature(F) " + dt.now().strftime("(%m/%d/%Y)"))
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (F)")

plt.scatter(cityWeatherdf["lat"],cityWeatherdf["temp"], marker = "o", edgecolors = "k",
            s=20, c="b",alpha=1.0,linewidth = 1 )
        
plt.xticks([-80,-60,-40,-20,0, 20,40,60,80,100])
plt.yticks([-100,-50,0,50,100,150])
plt.savefig("LatvsMaxTemp.png")
plt.show()   

```


![png](output_5_0.png)



```python
#Plot scatterplot for Latitude vs Humidity
plt.title("City Latitude vs Humidity " + dt.now().strftime("(%m/%d/%Y)"))
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")

plt.scatter(cityWeatherdf["lat"],cityWeatherdf["humidity"], marker = "o", edgecolors = "k",
            s=20, c="b",alpha=1.0,linewidth = 1 )
    
plt.xticks([-80,-60,-40,-20,0, 20,40,60,80,100])
plt.yticks([-20,20,40,60,80,100,120])
plt.savefig("LatvsHumidity.png")
plt.show()   
```


![png](output_6_0.png)



```python
#Plot scatterplot for Latitude vs Cloudiness
plt.title("City Latitude vs Cloudiness " + dt.now().strftime("(%m/%d/%Y)"))
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")

plt.scatter(cityWeatherdf["lat"],cityWeatherdf["cloudiness"], marker = "o", edgecolors = "k",
            s=20, c="b",alpha=1.0,linewidth = 1 )
    
plt.xticks([-80,-60,-40,-20,0, 20,40,60,80,100])
plt.yticks([0,20,40,60,80,100,120])
plt.savefig("LatvsCloudiness.png")
plt.show()  
```


![png](output_7_0.png)



```python
#Plot scatterplot for Latitude vs Wind Speed
plt.title("City Latitude vs Wind Speed " + dt.now().strftime("(%m/%d/%Y)"))
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")

plt.scatter(cityWeatherdf["lat"],cityWeatherdf["wind"], marker = "o", edgecolors = "k",
            s=20, c="b",alpha=1.0,linewidth = 1 )
    
#plt.xticks([-80,-60,-40,-20,0, 20,40,60,80,100])
#plt.yticks([-20,20,40,60,80,100,120])
plt.savefig("LatvsWindSpeed.png")
plt.show() 
```


![png](output_8_0.png)



```python

```
