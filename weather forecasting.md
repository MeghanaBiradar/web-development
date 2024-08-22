<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Dashboard</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="weather-dashboard">
        <h1>Weather Dashboard</h1>
        <div class="search-bar">
            <input type="text" id="location" placeholder="Enter location">
            <button id="search-button">Search</button>
        </div>
        <div class="weather-info">
            <h2 id="location-name"></h2>
            <p id="current-temperature"></p>
            <p id="weather-description"></p>
            <p id="humidity"></p>
            <p id="wind-speed"></p>
        </div>
        <div class="forecast">
            <h2>Forecast</h2>
            <div id="forecast-container"></div>
        </div>
    </div>
    <script src="script.js"></script>
</body>
</html>




//css
/* styles.css */
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    color: #333;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}

.weather-dashboard {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0,0,0,0.1);
    width: 300px;
}

.search-bar {
    display: flex;
    margin-bottom: 20px;
}

#location {
    flex: 1;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

#search-button {
    padding: 10px;
    border: 1px solid #007BFF;
    background-color: #007BFF;
    color: white;
    border-radius: 4px;
    cursor: pointer;
}

.weather-info, .forecast {
    margin-bottom: 20px;
}

#forecast-container {
    display: flex;
    flex-direction: column;
}

.forecast-item {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-bottom: 10px;
}
/////js script
// script.js
const apiKey = 'YOUR_API_KEY_HERE';  // Replace with your OpenWeatherMap API key
const apiUrl = 'https://api.openweathermap.org/data/2.5/';

document.getElementById('search-button').addEventListener('click', () => {
    const location = document.getElementById('location').value;
    if (location) {
        fetchWeatherData(location);
    }
});

async function fetchWeatherData(location) {
    try {
        // Fetch current weather data
        const currentWeatherResponse = await fetch(`${apiUrl}weather?q=${location}&appid=${apiKey}&units=metric`);
        const currentWeatherData = await currentWeatherResponse.json();
        
        if (currentWeatherData.cod === '404') {
            alert('Location not found');
            return;
        }
        
        displayCurrentWeather(currentWeatherData);

        // Fetch forecast data
        const forecastResponse = await fetch(`${apiUrl}forecast?q=${location}&appid=${apiKey}&units=metric`);
        const forecastData = await forecastResponse.json();
        displayForecast(forecastData);
        
    } catch (error) {
        console.error('Error fetching weather data:', error);
    }
}

function displayCurrentWeather(data) {
    document.getElementById('location-name').textContent = `${data.name}, ${data.sys.country}`;
    document.getElementById('current-temperature').textContent = `Temperature: ${data.main.temp} °C`;
    document.getElementById('weather-description').textContent = `Condition: ${data.weather[0].description}`;
    document.getElementById('humidity').textContent = `Humidity: ${data.main.humidity}%`;
    document.getElementById('wind-speed').textContent = `Wind Speed: ${data.wind.speed} m/s`;
}

function displayForecast(data) {
    const forecastContainer = document.getElementById('forecast-container');
    forecastContainer.innerHTML = '';
    
    data.list.forEach(item => {
        if (item.dt_txt.includes('12:00:00')) {  // Display only the forecast for midday
            const forecastItem = document.createElement('div');
            forecastItem.className = 'forecast-item';
            forecastItem.innerHTML = `
                <p>Date: ${item.dt_txt.split(' ')[0]}</p>
                <p>Temperature: ${item.main.temp} °C</p>
                <p>Condition: ${item.weather[0].description}</p>
            `;
            forecastContainer.appendChild(forecastItem);
        }
    });
}

