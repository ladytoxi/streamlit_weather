import streamlit as st
import pandas as pd
import requests

API_KEY=st.secrets["openweathermap"]["api_key"]

@st.cache_data(ttl=86400) #1 napig tárolja az adatokat
def weather_city(city):
    print(f"Able to reach {city}'s weather.")

    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric&lang=hu"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        st.error(f"Failed to fetch weather: {response.status_code} - {response.text}")
        return None

st.title("Robot Dreams Python - Weather Map & Data Visualization App")

city = st.text_input("Enter City name: (e.g.: Washington, London)", value = "Budapest")

data = weather_city(city)
print(data)

st.header(f'Current Weather in {city}')

def process_data(data):

    try:
        df = pd.DataFrame([{
            "lon": data["coord"]["lon"],
            "lat": data["coord"]["lat"],
            "temp" : data['main']["temp"],
            "humidity" : data['main']["humidity"],
            "speed" : data['wind']["speed"]
        }])
        df = df.sort_index()
        numeric_columns = ["temp", "humidity", "speed"]
        df[numeric_columns] = df[numeric_columns].astype(float)
        print(df)
        return df
    except Exception as e:
        st.error("No data available. Error: {e}")
        return None

if data:
    df = process_data(data)

    if df is not None:
        kpi1, kpi2, kpi3 = st.columns(3)
    
        #KPI
        with kpi1:
            st.metric(label="Temperature (°C)", value=f"{df['temp'].iloc[-1]:.2f} °C")
        with kpi2:
            st.metric(label="Humidity (%)", value=f"{df['humidity'].iloc[-1]:,.0f}%")
        with kpi3:
            st.metric(label="Wind speed (m/s)", value=f"{df['speed'][-30:].max():,.2f} m/s")

else:
    st.error(f"No data available. Check City!")

st.header(f'Weather map')
st.map(df)
