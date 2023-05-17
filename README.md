# python_API_challenge

Part 1: WeatherPy
In this deliverable, you'll create a Python script to visualize the weather of over 500 cities of varying distances from the equator. You'll use the citipy Python libraryLinks to an external site., the OpenWeatherMap APILinks to an external site., and your problem-solving skills to create a representative model of weather across cities.

For this part, you'll use the WeatherPy.ipynb Jupyter notebook provided in the starter code ZIP file. The starter code will guide you through the process of using your Python coding skills to develop a solution to address the required functionalities.

To get started, the code required to generate random geographic coordinates and the nearest city to each latitude and longitude combination is provided.

Requirement 1: Create Plots to Showcase the Relationship Between Weather Variables and Latitude
To fulfill the first requirement, you'll use the OpenWeatherMap API to retrieve weather data from the cities list generated in the starter code. Next, you'll create a series of scatter plots to showcase the following relationships:

Latitude vs. Temperature

Latitude vs. Humidity

Latitude vs. Cloudiness

Latitude vs. Wind Speed

Requirement 2: Compute Linear Regression for Each Relationship
To fulfill the second requirement, compute the linear regression for each relationship. Separate the plots into Northern Hemisphere (greater than or equal to 0 degrees latitude) and Southern Hemisphere (less than 0 degrees latitude). You may find it helpful to define a function in order to create the linear regression plots.

Next, create a series of scatter plots. Be sure to include the linear regression line, the model's formula, and the r values as you can see in the following image

Sample scatter plot with the linear regression line.
You should create the following plots:

Northern Hemisphere: Temperature vs. Latitude

Southern Hemisphere: Temperature vs. Latitude

Northern Hemisphere: Humidity vs. Latitude

Southern Hemisphere: Humidity vs. Latitude

Northern Hemisphere: Cloudiness vs. Latitude

Southern Hemisphere: Cloudiness vs. Latitude

Northern Hemisphere: Wind Speed vs. Latitude

Southern Hemisphere: Wind Speed vs. Latitude

After each pair of plots, explain what the linear regression is modeling. Describe any relationships that you notice and any other findings you may uncover.

Part 2: VacationPy
In this deliverable, you'll use your weather data skills to plan future vacations. Also, you'll use Jupyter notebooks, the geoViews Python library, and the Geoapify API.

The code needed to import the required libraries and load the CSV file with the weather and coordinates data for each city created in Part 1 is provided to help you get started.

Your main tasks will be to use the Geoapify API and the geoViews Python library and employ your Python skills to create map visualizations.

To succeed on this deliverable of the assignment, open the VacationPy.ipynb starter code and complete the following steps:

Create a map that displays a point for every city in the city_data_df DataFrame as shown in the following image. The size of the point should be the humidity in each city.

Humidity map
Narrow down the city_data_df DataFrame to find your ideal weather condition. For example:

A max temperature lower than 27 degrees but higher than 21

Wind speed less than 4.5 m/s

Zero cloudiness

NOTE
Feel free to adjust your specifications but make sure to set a reasonable limit to the number of rows returned by your API requests.

Create a new DataFrame called hotel_df to store the city, country, coordinates, and humidity.

For each city, use the Geoapify API to find the first hotel located within 10,000 meters of your coordinates.

Add the hotel name and the country as additional information in the hover message for each city on the map as in the following image:

Hotel map


WeatherPy
Starter Code to Generate Random Geographic Coordinates and a List of Cities
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
from scipy import stats
from scipy.stats import linregress
import time

from datetime import datetime
import scipy.stats as st
# Impor the OpenWeatherMap API key
from api_keys import weather_api_key

# Import citipy to determine the cities based on latitude and longitude
from citipy import citipy

#Output Data File (CSV)
output_data_file = "output_data/cities.csv"

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)
Generate the Cities List by Using the citipy Library
Empty list for holding the latitude and longitude combinations
lat_lngs = []

Empty list for holding the cities names
cities = []

Range of latitudes and longitudes
lat_range = (-90, 90) lng_range = (-180, 180)

Create a set of random lat and lng combinations
lats = np.random.uniform(lat_range[0], lat_range[1], size=1500) lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500) lat_lngs = zip(lats, lngs)

Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs: city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name

# If the city is unique, then add it to a our cities list
if city not in cities:
    cities.append(city)

Print the city count to confirm sufficient count
print(f"Number of cities in the list: {len(cities)}")

#Generate the Cities List by Using the citipy Library
#Empty list for holding the latitude and longitude combinations
lat_lngs = []

#Empty list for holding the cities names
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
lat_lngs = zip(lats, lngs)

saved_lats = []
saved_longs = []


#Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs: 
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name

# If the city is unique, then add it to a our cities list

    if city not in cities:
        cities.append(city)
        saved_lats.append(lat_lng[0])
        saved_longs.append(lat_lng[1])
#Print the city count to confirm sufficient count
print(f"Number of cities in the list: {len(cities)}")

city_list = pd.DataFrame({"City":cities, "Approx. Latitude":saved_lats, "Approx. Longitude":saved_longs})
city_list.head()
Number of cities in the list: 602
City	Approx. Latitude	Approx. Longitude
0	pali	25.726588	73.160064
1	northam	-28.531149	119.501519
2	adamstown	-54.601873	-127.113845
3	ushuaia	-84.579728	-63.904030
4	todos santos	23.464256	-109.948272
Requirement 1: Create Plots to Showcase the Relationship Between Weather Variables and Latitude
Use the OpenWeatherMap API to retrieve weather data from the cities list generated in the started code
# Set the API base URL
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

# Define an empty list to fetch the weather data for each city
city_data = []

# Print to logger
print("Beginning Data Retrieval     ")
print("-----------------------------")

# Create counters
record_count = 1
set_count = 1

# Loop through all the cities in our list to fetch weather data
for i, city in enumerate(cities):
        
    # Group cities in sets of 50 for logging purposes
    if (i % 50 == 0 and i >= 50):
        set_count += 1
        record_count = 0

    # Create endpoint URL with each city
    city_url = f"{url}q={city}&units={units}&appid=77a1b55fe8ca0310457bae0c85fcf767"  # Replace YOUR_API_KEY with your actual API key
    
    # Log the url, record, and set numbers
    print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))

    # Add 1 to the record count
    record_count += 1

    # Run an API request for each of the cities
    try:
        import requests

        # Send GET request to OpenWeatherMap API
        response = requests.get(city_url)
        
        # Parse the JSON and retrieve data
        city_weather = response.json()

        # Parse out latitude, longitude, max temp, humidity, cloudiness, wind speed, country, and date
        city_lat = city_weather['coord']['lat']
        city_lng = city_weather['coord']['lon']
        city_max_temp = city_weather['main']['temp_max']
        city_humidity = city_weather['main']['humidity']
        city_clouds = city_weather['clouds']['all']
        city_wind = city_weather['wind']['speed']
        city_country = city_weather['sys']['country']
        city_date = city_weather['dt']

        # Append the City information into city_data list
        city_data.append({"City": city, 
                          "Lat": city_lat, 
                          "Lng": city_lng, 
                          "Max Temp": city_max_temp,
                          "Humidity": city_humidity,
                          "Cloudiness": city_clouds,
                          "Wind Speed": city_wind,
                          "Country": city_country,
                          "Date": city_date})

    # If an error is experienced, skip the city
    except:
        print("City not found. Skipping...")
        pass
              
# Indicate that Data Loading is complete 
print("-----------------------------")
print("Data Retrieval Complete      ")
print("-----------------------------")
Beginning Data Retrieval     
-----------------------------
Processing Record 1 of Set 1 | pali
Processing Record 2 of Set 1 | northam
Processing Record 3 of Set 1 | adamstown
Processing Record 4 of Set 1 | ushuaia
Processing Record 5 of Set 1 | todos santos
Processing Record 6 of Set 1 | waitangi
Processing Record 7 of Set 1 | blackmans bay
Processing Record 8 of Set 1 | newman
...........
City not found. Skipping...
Processing Record 1 of Set 12 | malvan
Processing Record 2 of Set 12 | golden gate
Processing Record 3 of Set 12 | caister-on-sea
Processing Record 4 of Set 12 | fayroz koh
Processing Record 5 of Set 12 | corralejo
Processing Record 6 of Set 12 | ancud
Processing Record 7 of Set 12 | kolonia town
Processing Record 8 of Set 12 | al bardiyah
Processing Record 9 of Set 12 | garissa
Processing Record 10 of Set 12 | viligili
City not found. Skipping...
Processing Record 11 of Set 12 | brisas de zicatela
Processing Record 12 of Set 12 | padangsidempuan
Processing Record 13 of Set 12 | xilin hot
Processing Record 14 of Set 12 | flin flon
Processing Record 15 of Set 12 | krumovgrad
Processing Record 16 of Set 12 | nar'yan-mar
Processing Record 17 of Set 12 | tepecoacuilco de trujano
Processing Record 18 of Set 12 | betio village
Processing Record 19 of Set 12 | porto novo
Processing Record 20 of Set 12 | obo
Processing Record 21 of Set 12 | calbayog city
Processing Record 22 of Set 12 | ambalangoda
Processing Record 23 of Set 12 | guiuan
Processing Record 24 of Set 12 | gulf shores
Processing Record 25 of Set 12 | kataragama
City not found. Skipping...
Processing Record 26 of Set 12 | ati
Processing Record 27 of Set 12 | bonthe
Processing Record 28 of Set 12 | fuvahmulah
City not found. Skipping...
Processing Record 29 of Set 12 | paros
Processing Record 30 of Set 12 | uvel'skiy
Processing Record 31 of Set 12 | dompu
Processing Record 32 of Set 12 | aljezur
Processing Record 33 of Set 12 | mizdah
Processing Record 34 of Set 12 | skeldon
Processing Record 35 of Set 12 | hadibu
Processing Record 36 of Set 12 | tongren
Processing Record 37 of Set 12 | san julian
Processing Record 38 of Set 12 | karyes
Processing Record 39 of Set 12 | malpica
Processing Record 40 of Set 12 | mazagao
Processing Record 41 of Set 12 | al ghayzah
Processing Record 42 of Set 12 | san luis de la loma
Processing Record 43 of Set 12 | vernal
Processing Record 44 of Set 12 | dalnegorsk
Processing Record 45 of Set 12 | chiang kham
Processing Record 46 of Set 12 | puerto san jose
Processing Record 47 of Set 12 | bheramara
Processing Record 48 of Set 12 | karachev
Processing Record 49 of Set 12 | khombole
Processing Record 0 of Set 13 | mirpur sakro
Processing Record 1 of Set 13 | mckinleyville
-----------------------------
Data Retrieval Complete      
-----------------------------
# Convert the cities weather data into a Pandas DataFrame
city_data_df =pd.DataFrame(city_data)

# Show Record Count
city_data_df.count()
City          576
Cloudiness    576
Country       576
Date          576
Humidity      576
Lat           576
Lng           576
Max Temp      576
Wind Speed    576
dtype: int64
# Display sample data
city_data_df.head()
City	Cloudiness	Country	Date	Humidity	Lat	Lng	Max Temp	Wind Speed
0	pali	0	IN	1683588048	14	25.7667	73.3333	84.11	6.15
1	northam	100	GB	1683588002	98	51.0333	-4.2167	54.61	7.85
2	adamstown	100	PN	1683588048	56	-25.0660	-130.1015	70.84	13.29
3	ushuaia	75	AR	1683588048	57	-54.8000	-68.3000	47.86	13.80
4	todos santos	12	MX	1683588049	68	23.4500	-110.2167	72.45	10.85
# Export the City_Data into a csv
city_data_df.to_csv("../output_data/cities.csv", index_label="City_ID")
# Read saved data
city_data_df = pd.read_csv("../output_data/cities.csv", index_col="City_ID")

# Display sample data
city_data_df.head()
City	Cloudiness	Country	Date	Humidity	Lat	Lng	Max Temp	Wind Speed
City_ID									
0	pali	0	IN	1683588048	14	25.7667	73.3333	84.11	6.15
1	northam	100	GB	1683588002	98	51.0333	-4.2167	54.61	7.85
2	adamstown	100	PN	1683588048	56	-25.0660	-130.1015	70.84	13.29
3	ushuaia	75	AR	1683588048	57	-54.8000	-68.3000	47.86	13.80
4	todos santos	12	MX	1683588049	68	23.4500	-110.2167	72.45	10.85
Create the Scatter Plots Requested
Latitude Vs. Temperature
# Build scatter plot for latitude vs. temperature
# YOUR CODE HERE
latit_vs_Temp_axes = city_data_df.plot(kind="scatter", x="Lat", y="Max Temp", 
                                      title="Maximum Temperature (F) vs. City Latitude")

latit_vs_Temp_axes.get_figure().savefig("../output_data/Temp_vs_Latitude_All_Cities.png")
# Incorporate the other graph properties
# YOUR CODE HERE

# Save the figure
plt.savefig("../output_data/Fig1.png")

# Show plot
plt.show()
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/7df0fa07-f54e-4162-8ea0-a20215a8a82a)

Latitude Vs. Humidity
# Build the scatter plots for latitude vs. humidity
# YOUR CODE HERE
latit_vs_Hum_axes = city_data_df.plot(kind="scatter", x="Lat", y="Humidity", 
                                      title="Humidity  vs. City Latitude")

latit_vs_Hum_axes.get_figure().savefig("../output_data/Temp_vs_Humidity_All_Cities.png")
# Incorporate the other graph properties
# YOUR CODE HERE

# Save the figure
plt.savefig("../output_data/Fig2.png")

# Show plot
plt.show()
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/dde70d91-017a-4473-804e-b74b4a7fabb0)

Latitude Vs. Cloudiness
# Build the scatter plots for latitude vs. cloudiness
# YOUR CODE HERE
latit_vs_Cloudiness_axes = city_data_df.plot(kind="scatter", x="Lat", y="Cloudiness", 
                                      title="Cloudiness vs. City Latitude")

latit_vs_Hum_axes.get_figure().savefig("../output_data/Lat_vs_Cloudiness_All_Cities.png")
# Incorporate the other graph properties
# YOUR CODE HERE

# Save the figure
plt.savefig("../output_data/Fig3.png")

# Show plot
plt.show()
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/270bd312-fd8b-41aa-a437-bc7149543516)

Latitude vs. Wind Speed Plot
# Build the scatter plots for latitude vs. wind speed
# YOUR CODE HERE
latit_vs_wind_axes = city_data_df.plot(kind="scatter", x="Lat", y="Wind Speed", 
                                      title="Wind Speed vs. City Latitude")

latit_vs_Hum_axes.get_figure().savefig("../output_data/Lat_vs_Wind_All_Cities.png")
# Incorporate the other graph properties
# YOUR CODE HERE

# Save the figure
plt.savefig("../output_data/Fig4.png")

# Show plot
plt.show()
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/a344a646-23cd-4a80-80a2-a9a3116fcf9e)

Requirement 2: Compute Linear Regression for Each Relationship
# Define a function to create Linear Regression plots
# YOUR CODE HERE

def createLinearRegressionPlot(x_values, y_values, x_label, y_label, hemisphere, line_placement, ylim=None):
    (slope, intercept, rvalue, pvalue, stderr) = stats.linregress(x_values, y_values)
    regress_values = x_values * slope + intercept
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))
    
    
    plt.scatter(x_values, y_values)
    plt.xlabel(x_label)
    plt.ylabel(y_label)
    if ylim is not None:
        plt.ylim(0, ylim)
    plt.title(f"{hemisphere} Hemisphere - {x_label} vs {y_label}")
    plt.annotate(line_eq,line_placement,fontsize=15,color="red")
    
    print(f"The r-squared is: {rvalue**2}")
    correlation = stats.pearsonr(x_values, y_values)
    print(f"The correlation between both factors is {round(correlation[0],2)}")
    return plt.plot(x_values,regress_values,"r-")
# Create a DataFrame with the Northern Hemisphere data (Latitude >= 0)
# YOUR CODE HERE
northern_hemi_df = city_data_df.loc[city_data_df["Lat"] >= 0]

# Display sample data
northern_hemi_df.head()
City	Cloudiness	Country	Date	Humidity	Lat	Lng	Max Temp	Wind Speed
City_ID									
0	pali	0	IN	1683588048	14	25.7667	73.3333	84.11	6.15
1	northam	100	GB	1683588002	98	51.0333	-4.2167	54.61	7.85
4	todos santos	12	MX	1683588049	68	23.4500	-110.2167	72.45	10.85
7	newman	16	US	1683588044	47	37.3138	-121.0208	77.34	3.91
8	happy valley-goose bay	75	CA	1683587964	60	53.3168	-60.3315	37.71	16.11
# Create a DataFrame with the Southern Hemisphere data (Latitude < 0)
# YOUR CODE HERE
southern_hemi_df = city_data_df.loc[city_data_df["Lat"] < 0]
# Display sample data
southern_hemi_df.head()
City	Cloudiness	Country	Date	Humidity	Lat	Lng	Max Temp	Wind Speed
City_ID									
2	adamstown	100	PN	1683588048	56	-25.0660	-130.1015	70.84	13.29
3	ushuaia	75	AR	1683588048	57	-54.8000	-68.3000	47.86	13.80
5	waitangi	100	NZ	1683587913	95	-43.9535	-176.5597	60.82	8.99
6	blackmans bay	93	AU	1683588049	69	-43.0167	147.3167	56.73	13.80
9	port lincoln	31	AU	1683588049	76	-34.7333	135.8667	57.70	5.10
Temperature vs. Latitude Linear Regression Plot
# Linear regression on Northern Hemisphere
# YOUR CODE HERE
x_values = northern_hemi_df["Lat"]
y_values = northern_hemi_df["Max Temp"]
createLinearRegressionPlot(x_values, y_values, "Lat", "Max Temp (F)", "Northern",(40, 20))
The r-squared is: 0.7009193762211541
The correlation between both factors is -0.84
[<matplotlib.lines.Line2D at 0x222a6c25f28>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/23eb74d1-5c11-4f88-84a0-1ef52001df9e)

# Linear regression on Southern Hemisphere
# YOUR CODE HERE
x_values = southern_hemi_df["Lat"]
y_values = southern_hemi_df["Max Temp"]
createLinearRegressionPlot(x_values, y_values, "Lat", "Max Temp (F)", "Southern",(-30, 50))
The r-squared is: 0.5994967246394721
The correlation between both factors is 0.77
[<matplotlib.lines.Line2D at 0x222a6aa5dd8>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/9944e31c-8d7b-4767-a84a-680a4ef3e303)

Discussion about the linear relationship: YOUR RESPONSE HERE The Northern hemisphere has a negative correlation with the the maximum temperature whereas in the Southern hemosphere there is a positive correlation with maximum temperature

Humidity vs. Latitude Linear Regression Plot
# Northern Hemisphere
# YOUR CODE HERE
x_values = northern_hemi_df['Lat']
y_values = northern_hemi_df['Humidity']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Humidity (%)", "Northern",(40, 20))
The r-squared is: 0.002781686489055418
The correlation between both factors is 0.05
[<matplotlib.lines.Line2D at 0x222a6914470>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/ff57afd9-e35f-4171-904b-26db8046b4a0)

# Southern Hemisphere
# YOUR CODE HERE
x_values = southern_hemi_df['Lat']
y_values = southern_hemi_df['Humidity']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Humidity (%)", "Northern",(-30, 50))
The r-squared is: 0.008010159680846783
The correlation between both factors is 0.09
[<matplotlib.lines.Line2D at 0x222a6870438>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/0adecc8f-e3b1-490d-8b00-e5bb814784a2)

Discussion about the linear relationship: YOUR RESPONSE HERE There is no correlation between humidity and maximum temperature in Northern or Southern Hemisphere

Cloudiness vs. Latitude Linear Regression Plot
# Northern Hemisphere
# YOUR CODE HERE
x_values = northern_hemi_df['Lat']
y_values = northern_hemi_df['Cloudiness']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Cloudiness(%)", "Northern",(40, 20))
The r-squared is: 0.005178301338383147
The correlation between both factors is 0.07
[<matplotlib.lines.Line2D at 0x222a6ff0080>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/0c5a98a8-ba52-42f2-8f8c-0a928e21e1f0)

# Southern Hemisphere
# YOUR CODE HERE
x_values = southern_hemi_df['Lat']
y_values = southern_hemi_df['Cloudiness']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Cloudiness(%)", "Northern",(-30, 50))
The r-squared is: 0.006149937767688946
The correlation between both factors is -0.08
[<matplotlib.lines.Line2D at 0x222a6e728d0>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/07a3f56a-263d-4f94-9598-4b4f264e473c)

Discussion about the linear relationship: YOUR RESPONSE HERE There no correlation between Cloudiness and maximum temperature in Northern or Southern Hemisphere. Perhaps a slight positive correlation for Northern hemisphere and a slight negative correlation for Southern Hemisphere.

Wind Speed vs. Latitude Linear Regression Plot
# Northern Hemisphere
# YOUR CODE HERE
x_values = northern_hemi_df['Lat']
y_values = northern_hemi_df['Wind Speed']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Wind Speed", "Northern",(40, 20))
The r-squared is: 0.00207890326517062
The correlation between both factors is 0.05
[<matplotlib.lines.Line2D at 0x222a6699860>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/231a3600-1584-4315-b0c0-ccf098e7d5d0)

# Southern Hemisphere
# YOUR CODE HERE
x_values = southern_hemi_df['Lat']
y_values = southern_hemi_df['Wind Speed']
createLinearRegressionPlot(x_values, y_values, "Latitude", "Wind Speed", "Souththern",(-40, 25))
The r-squared is: 0.027642039390237565
The correlation between both factors is -0.17
[<matplotlib.lines.Line2D at 0x222a728c9b0>]
![image](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/e83318e0-9b0f-434b-94a1-ac5533073816)

Discussion about the linear relationship: YOUR RESPONSE HERE

There is no correlation between wind speed and maximum temperature in Northern or Southern Hemisphere


VacationPy
Starter Code to Import Libraries and Load the Weather and Coordinates Data
# Dependencies and Setup
import os

import pandas as pd
import requests
import hvplot.pandas

import geoviews


# import geoviews.tile_sources as gvts
# import geoviews as gv
# Import API key
from api_keys import geoapify_key
# Load the CSV file created in Part 1 into a Pandas DataFrame



#Output Data File (CSV)
output_data_file = "../output_data/cities.csv"
city_data_df = pd.read_csv("../output_data/cities.csv")
# Display sample data
city_data_df.head()
City_ID	City	Lat	Lng	Max Temp	Humidity	Cloudiness	Wind Speed	Country	Date
0	0	sikonge	-5.6333	32.7667	66.63	74	19	2.91	TZ	1684189323
1	1	aasiaat	68.7098	-52.8699	29.43	75	100	8.30	GL	1684189323
2	2	alice springs	-23.7000	133.8833	42.35	100	1	6.29	AU	1684189323
3	3	la passe	45.5549	-0.8967	55.63	94	100	13.51	FR	1684189323
4	4	adamstown	-25.0660	-130.1015	75.56	85	1	21.59	PN	1684189324
Step 1: Create a map that displays a point for every city in the city_data_df DataFrame. The size of the point should be the humidity in each city.
# Configure the map plot


city_data_df.hvplot.points('Lng', 'Lat', size = 'Humidity', geo=True, color='City', tiles=True, scale=.9, frame_height=500 )

# Display the map


# YOUR CODE HERE
#
#
Step 2: Narrow down the city_data_df DataFrame to find your ideal weather condition
# Narrow down cities that fit criteria and drop any results with null values
# YOUR CODE HERE
ideal_weather = city_data_df[(city_data_df['Max Temp'] > 18) & (city_data_df['Humidity'] < 80)&(city_data_df['Cloudiness']<40)]
ideal_weather.head()
# Drop any rows with null values
# YOUR CODE HERE

# Display sample data
# YOUR CODE HERE
City_ID	City	Lat	Lng	Max Temp	Humidity	Cloudiness	Wind Speed	Country	Date
0	0	sikonge	-5.6333	32.7667	66.63	74	19	2.91	TZ	1684189323
8	8	am timan	11.0297	20.2827	93.00	24	15	4.50	TD	1684189325
13	13	ulladulla	-35.3500	150.4667	53.74	73	0	5.57	AU	1684189327
15	15	whitehorse	60.7161	-135.0538	64.09	21	20	4.61	CA	1684189063
17	17	arris	35.2588	6.3471	45.64	64	3	5.53	DZ	1684189328
Step 3: Create a new DataFrame called hotel_df.
# Use the Pandas copy function to create DataFrame called hotel_df to store the city, country, coordinates, and humidity
# YOUR CODE HERE
hotel_df = ideal_weather[['City','Country', 'Lat', 'Lng','Humidity']].copy()
# Add an empty column, "Hotel Name," to the DataFrame so you can store the hotel found using the Geoapify API
# YOUR CODE HERE
hotel_df['Hotel Name']=" "
# Display sample data
hotel_df.head()
# YOUR CODE HERE
City	Country	Lat	Lng	Humidity	Hotel Name
0	sikonge	TZ	-5.6333	32.7667	74	
8	am timan	TD	11.0297	20.2827	24	
13	ulladulla	AU	-35.3500	150.4667	73	
15	whitehorse	CA	60.7161	-135.0538	21	
17	arris	DZ	35.2588	6.3471	64	
Step 4: For each city, use the Geoapify API to find the first hotel located within 10,000 metres of your coordinates.
# Set parameters to search for a hotel
radius = 10000
params = {
    
}

# Print a message to follow up the hotel search
print("Starting hotel search")

# Iterate through the hotel_df DataFrame
for index, row in hotel_df.iterrows():
    # get latitude, longitude from the DataFrame
    # YOUR CODE HERE
    lat = row[2]                                        # hotel_df['Lat'].[index]   
    lng = row[3]                                        # hotel_df['Lng'].[index]
    
    # Add filter and bias parameters with the current city's latitude and longitude to the params dictionary
   
    params['categories'] = 'accommodation.hotel'
    params["filter"] =  f"circle:{lng},{lat},{radius}"                    
    params["bias"] = f"proximity:{lng},{lat}"                  
    params['limit'] = 1
    params["apiKey"] = geoapify_key 
    # Set base URL
    base_url = "https://api.geoapify.com/v2/places"


    # Make and API request using the params dictionaty
    name_address = requests.get(base_url, params=params)
    
    # Convert the API response to JSON format
    name_address = name_address.json() # YOUR CODE HERE
    
    # Grab the first hotel from the results and store the name in the hotel_df DataFrame
    try:
        hotel_df.loc[index, "Hotel Name"] = name_address["features"][0]["properties"]["name"]
    except (KeyError, IndexError):
        # If no hotel is found, set the hotel name as "No hotel found".
        hotel_df.loc[index, "Hotel Name"] = "No hotel found"
        
    # Log the search results
    print(f"{hotel_df.loc[index, 'City']} - nearest hotel: {hotel_df.loc[index, 'Hotel Name']}")

# Display sample data
hotel_df
Starting hotel search
sikonge - nearest hotel: Kaluvya hotel and shopping center
am timan - nearest hotel: Nimla
ulladulla - nearest hotel: Hotel Marlin
whitehorse - nearest hotel: Town & Mountain Hotel
arris - nearest hotel: No hotel found
port alfred - nearest hotel: No hotel found
barah - nearest hotel: No hotel found
jamestown - nearest hotel: DoubleTree Jamestown
blackmans bay - nearest hotel: Villa Howden
....................................................
17	arris	DZ	35.2588	6.3471	64	No hotel found
...	...	...	...	...	...	...
538	florence	IT	43.7667	11.2500	66	Old bridge Apartment
541	kovalam	IN	8.3667	76.9967	79	Agastya Ayurveda Hotel
542	blackwater	AU	-23.5833	148.8833	77	No hotel found
544	hammerfest	NO	70.6634	23.6821	66	Thon Hotel Hammerfest
546	griffith	US	41.5284	-87.4237	44	Residence Inn by Marriott Chicago Southeast/Ha...
150 rows × 6 columns
![Picture1](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/30ef30e9-c858-4970-91c5-3ade4fba6afb)

url_test = 'https://api.geoapify.com/v2/places?categories=accommodation.hotel&filter=circle:-0.07071648508463113,51.50848194136378,1000&bias=proximity:-0.07071648508463113,51.50848194136378&limit=20&apiKey=' + geoapify_key
print(url_test)
https://api.geoapify.com/v2/places?categories=accommodation.hotel&filter=circle:-0.07071648508463113,51.50848194136378,1000&bias=proximity:-0.07071648508463113,51.50848194136378&limit=20&apiKey=04d594a52dcc4614bd562fcadca136e1
Step 5: Add the hotel name and the country as additional information in the hover message for each city in the map.
%%capture --no-display

# Configure the map plot
# YOUR CODE HERE

# Display the map
# YOUR CODE HERE

hotel_df.hvplot.points('Lng', 'Lat', size = 'Humidity', geo=True, color='City', tiles=True, scale=.9, frame_height=500, hover_cols=['City', 'Hotel Name', 'Country'])
 
 
![Picture2](https://github.com/SteliosKosmidis/python_API_challenge/assets/125541671/4ca634bd-f0ab-4990-96d0-47617619d7a7)

