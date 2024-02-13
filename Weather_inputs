import openmeteo_requests
import requests_cache
import pandas as pd
from retry_requests import retry
from geopy.geocoders import Nominatim

# Setup the Open-Meteo API client with cache and retry on error
cache_session = requests_cache.CachedSession('.cache', expire_after=3600)
retry_session = retry(cache_session, retries=5, backoff_factor=0.2)
openmeteo = openmeteo_requests.Client(session=retry_session)

# Geocode the location using the postcode
geolocator = Nominatim(user_agent="Patrick")
location_input = input ('Enter location:')
location = geolocator.geocode(location_input)
latitude = location.latitude
longitude = location.longitude
forecatedays = input('Enter Forecast Days: ')

# Make sure all required weather variables are listed here
# The order of variables in hourly or daily is important to assign them correctly below
url = "https://api.open-meteo.com/v1/forecast"
params = {
    "latitude": latitude,
    "longitude": longitude,
    "hourly": "temperature_2m",
    "forecast_days": forecatedays
}
responses = openmeteo.weather_api(url, params=params)

# Process first location. Add a for-loop for multiple locations or weather models
response = responses[0]

# Process hourly data
hourly = response.Hourly()
hourly_temperature_2m = hourly.Variables(0).ValuesAsNumpy()

# Create DataFrame for hourly data
hourly_data = {"date": pd.date_range(
    start=pd.to_datetime(hourly.Time(), unit="s"),
    end=pd.to_datetime(hourly.TimeEnd(), unit="s"),
    freq=pd.Timedelta(seconds=hourly.Interval()),
    inclusive="left"
)}
hourly_data["temperature_2m"] = hourly_temperature_2m
hourly_dataframe = pd.DataFrame(data=hourly_data)

# Print the geocoded location information
print(f"Location: {location.address}")
print(f"Latitude: {latitude}, Longitude: {longitude}")
print(location.raw)

# Print the hourly weather forecast DataFrame
print(hourly_dataframe)
