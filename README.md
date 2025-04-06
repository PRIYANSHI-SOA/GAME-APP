<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
    <style>
        /* General Styles */
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background: #e0f7fa;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
            transition: background-color 0.3s ease;
        }

        h1 {
            font-size: 3rem;
            margin-bottom: 20px;
            color: #004d40;
            text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
        }

        .container {
            background-color: #ffffff;
            border-radius: 12px;
            padding: 30px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 320px;
            max-width: 100%;
            margin-bottom: 20px;
        }

        .input-field {
            width: 80%;
            padding: 12px;
            font-size: 1.1rem;
            margin-bottom: 20px;
            border: 2px solid #004d40;
            border-radius: 8px;
            outline: none;
            transition: border-color 0.3s;
        }

        .input-field:focus {
            border-color: #00796b;
        }

        .btn {
            padding: 12px 25px;
            background-color: #00796b;
            color: #fff;
            font-size: 1.2rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        .btn:hover {
            background-color: #004d40;
        }

        .weather-info {
            margin-top: 20px;
        }

        .temp {
            font-size: 2.5rem;
            font-weight: bold;
            color: #00796b;
        }

        .description {
            font-size: 1.3rem;
            text-transform: capitalize;
            margin: 10px 0;
        }

        img {
            margin-top: 15px;
            width: 80px;
        }

        .error {
            color: red;
            font-size: 1.1rem;
            margin-top: 10px;
        }

        .loading {
            font-size: 1.2rem;
            color: #00796b;
            font-weight: bold;
            display: none;
        }

        .forecast {
            margin-top: 20px;
            display: flex;
            justify-content: space-evenly;
            flex-wrap: wrap;
        }

        .forecast-item {
            text-align: center;
            background-color: #ffffff;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            margin: 5px;
            width: 80px;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .forecast-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }

        .forecast-item img {
            width: 50px;
            height: 50px;
        }

        .forecast-item p {
            margin: 5px 0;
        }

        .back-btn {
            margin-top: 20px;
            padding: 10px 20px;
            background-color: #004d40;
            color: #fff;
            font-size: 1.2rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s ease;
            display: none;
        }

        .back-btn:hover {
            background-color: #00796b;
        }

        .search-history {
            margin-top: 20px;
            font-size: 1.1rem;
            color: #00796b;
        }

        .history-item {
            cursor: pointer;
            padding: 10px;
            margin: 5px;
            border-radius: 8px;
            border: 1px solid #00796b;
            transition: background-color 0.3s ease;
        }

        .history-item:hover {
            background-color: #00796b;
            color: white;
        }

        .delete-history-btn {
            padding: 10px 20px;
            background-color: #d32f2f;
            color: #fff;
            font-size: 1.2rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s ease;
            margin-top: 10px;
        }

        .delete-history-btn:hover {
            background-color: #b71c1c;
        }

        .scroll-btn {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #00796b;
            color: white;
            padding: 10px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 1.5rem;
            transition: background-color 0.3s ease;
        }

        .scroll-btn:hover {
            background-color: #004d40;
        }

        @media (max-width: 500px) {
            .container {
                width: 90%;
            }

            .forecast-item {
                width: 45%;
                margin: 10px 0;
            }
        }
    </style>
</head>

<body>
    <h1>Weather App</h1>

    <div class="container" id="weather-container">
        <input type="text" id="city-input" class="input-field" placeholder="Enter City Name" />
        <button class="btn" onclick="getWeather()">Get Weather</button>
        <button class="btn" onclick="getGeolocationWeather()">Use Current Location</button>

        <div class="weather-info">
            <p id="weather-description" class="description"></p>
            <p id="temperature" class="temp"></p>
            <p id="humidity"></p>
            <p id="pressure"></p>
            <p id="wind-speed"></p>
            <img id="weather-icon" src="" alt="" />
            <p id="error-message" class="error"></p>
            <p id="loading-message" class="loading">Loading...</p>
        </div>

        <div class="forecast" id="forecast"></div>

        <div class="search-history" id="search-history">
            <h3>Search History</h3>
            <!-- History will be populated here -->
        </div>

        <button class="delete-history-btn" id="delete-history-btn" onclick="deleteSearchHistory()">Delete History</button>

        <button class="back-btn" id="back-btn" onclick="backToNormal()">Back</button>
    </div>

    <button class="scroll-btn" id="scroll-top-btn" onclick="scrollToTop()">↑</button>
    <button class="scroll-btn" id="scroll-bottom-btn" onclick="scrollToBottom()">↓</button>

    <script>
        const apiKey = 'cbbcaee26193ed9672cdede533f382d5';  // Replace with your actual API Key
        let searchHistory = JSON.parse(localStorage.getItem('searchHistory')) || [];

        function updateSearchHistory() {
            const historyContainer = document.getElementById('search-history');
            historyContainer.innerHTML = '<h3>Search History</h3>';
            searchHistory.forEach((city, index) => {
                const historyItem = document.createElement('div');
                historyItem.classList.add('history-item');
                historyItem.textContent = city;
                historyItem.onclick = () => getWeather(city);
                historyContainer.appendChild(historyItem);
            });
        }

        function deleteSearchHistory() {
            // Clear the history from localStorage and in-memory
            localStorage.removeItem('searchHistory');
            searchHistory = [];

            // Clear the weather info
            const weatherInfo = document.getElementById('weather-description');
            const temperature = document.getElementById('temperature');
            const humidity = document.getElementById('humidity');
            const pressure = document.getElementById('pressure');
            const windSpeed = document.getElementById('wind-speed');
            const weatherIcon = document.getElementById('weather-icon');
            const errorMessage = document.getElementById('error-message');
            const forecast = document.getElementById('forecast');

            weatherInfo.textContent = '';
            temperature.textContent = '';
            humidity.textContent = '';
            pressure.textContent = '';
            windSpeed.textContent = '';
            weatherIcon.src = '';
            errorMessage.textContent = '';
            forecast.innerHTML = '';

            // Hide the "Back" button
            const backBtn = document.getElementById('back-btn');
            backBtn.style.display = 'none';

            // Update the search history UI
            updateSearchHistory();
        }

        async function getWeather(city = '') {
            const cityInput = document.getElementById('city-input');
            const weatherInfo = document.getElementById('weather-description');
            const temperature = document.getElementById('temperature');
            const weatherIcon = document.getElementById('weather-icon');
            const humidity = document.getElementById('humidity');
            const pressure = document.getElementById('pressure');
            const windSpeed = document.getElementById('wind-speed');
            const errorMessage = document.getElementById('error-message');
            const loadingMessage = document.getElementById('loading-message');
            const forecast = document.getElementById('forecast');
            const backBtn = document.getElementById('back-btn');

            if (!city && !cityInput.value) {
                errorMessage.textContent = 'Please enter a city name!';
                return;
            }

            city = city || cityInput.value;
            loadingMessage.style.display = 'block';
            errorMessage.textContent = '';
            weatherInfo.textContent = '';
            temperature.textContent = '';
            humidity.textContent = '';
            pressure.textContent = '';
            windSpeed.textContent = '';
            weatherIcon.src = '';
            forecast.innerHTML = '';  // Clear previous forecast

            // Add to search history
            if (!searchHistory.includes(city)) {
                searchHistory.push(city);
                localStorage.setItem('searchHistory', JSON.stringify(searchHistory));
            }

            updateSearchHistory();

            const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${apiKey}`;
            const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;

            try {
                const response = await fetch(url);
                const data = await response.json();

                if (data.cod === '404') {
                    errorMessage.textContent = 'City not found!';
                    loadingMessage.style.display = 'none';
                } else {
                    const temp = data.main.temp.toFixed(1);
                    const description = data.weather[0].description;
                    const iconCode = data.weather[0].icon;
                    const iconUrl = `http://openweathermap.org/img/wn/${iconCode}.png`;

                    weatherInfo.textContent = description.charAt(0).toUpperCase() + description.slice(1);
                    temperature.textContent = `${temp}°C`;
                    humidity.textContent = `Humidity: ${data.main.humidity}%`;
                    pressure.textContent = `Pressure: ${data.main.pressure} hPa`;
                    windSpeed.textContent = `Wind Speed: ${data.wind.speed} m/s`;
                    weatherIcon.src = iconUrl;

                    // Fetch 5-day forecast
                    const forecastResponse = await fetch(forecastUrl);
                    const forecastData = await forecastResponse.json();

                    forecastData.list.slice(0, 5).forEach(item => {
                        const forecastItem = document.createElement('div');
                        forecastItem.classList.add('forecast-item');
                        const date = new Date(item.dt * 1000);
                        const time = date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
                        const forecastIcon = `http://openweathermap.org/img/wn/${item.weather[0].icon}.png`;

                        forecastItem.innerHTML = `
                            <p>${time}</p>
                            <img src="${forecastIcon}" alt="${item.weather[0].description}">
                            <p>${item.main.temp.toFixed(1)}°C</p>
                        `;
                        forecast.appendChild(forecastItem);
                    });

                    loadingMessage.style.display = 'none';
                    backBtn.style.display = 'block';
                }
            } catch (error) {
                console.error('Error fetching weather data:', error);
                errorMessage.textContent = 'Error fetching weather data!';
                loadingMessage.style.display = 'none';
            }
        }

        async function getGeolocationWeather() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(position => {
                    const lat = position.coords.latitude;
                    const lon = position.coords.longitude;
                    const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`;

                    fetch(url)
                        .then(response => response.json())
                        .then(data => {
                            const city = data.name;
                            getWeather(city);
                        });
                });
            } else {
                alert('Geolocation is not supported by this browser.');
            }
        }

        function backToNormal() {
            const backBtn = document.getElementById('back-btn');
            const weatherContainer = document.getElementById('weather-container');
            weatherContainer.scrollIntoView({ behavior: 'smooth' });
            backBtn.style.display = 'none';
        }

        function scrollToTop() {
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function scrollToBottom() {
            window.scrollTo({ top: document.body.scrollHeight, behavior: 'smooth' });
        }

        // Initialize app with search history
        updateSearchHistory();
    </script>
</body>

</html>
