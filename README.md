[index.html](https://github.com/user-attachments/files/26417411/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Kiosk Dashboard</title>
    <style>
        :root {
            --bg-color: #0f1115;
            --panel-bg: #1e2128;
            --text-main: #f0f0f0;
            --text-muted: #a0aab2;
            --accent-blue: #0a2351; 
            --accent-yellow: #ffc52f; 
            --accent-kristi: #ff7675; 
            --accent-jake: #00b894;   
            --accent-ryan: #a29bfe;   
            --border-radius: 16px;
            --font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            font-family: var(--font-family);
            margin: 0;
            padding: 20px;
            height: 100vh;
            box-sizing: border-box;
            overflow: hidden; 
        }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: rgba(0,0,0,0.2); border-radius: 4px; }
        ::-webkit-scrollbar-thumb { background: var(--text-muted); border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--accent-yellow); }

        .dashboard-container {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            grid-template-rows: auto 1fr;
            gap: 20px;
            height: 100%;
        }

        .header {
            grid-column: 1 / -1;
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            padding: 0 10px 10px 10px;
            border-bottom: 2px solid var(--panel-bg);
        }

        .time { font-size: 4rem; font-weight: bold; line-height: 1; color: var(--accent-yellow); }
        .date { font-size: 1.5rem; color: var(--text-muted); }

        .panel {
            background-color: var(--panel-bg);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.3);
            display: flex;
            flex-direction: column;
            overflow: hidden; 
        }

        h2 {
            margin-top: 0; font-size: 1.5rem; border-bottom: 1px solid #333;
            padding-bottom: 10px; color: var(--accent-yellow);
            display: flex; align-items: center; gap: 10px;
            flex-shrink: 0; 
        }

        .col-wrapper {
            display: flex;
            flex-direction: column;
            gap: 20px;
            height: 100%;
            overflow: hidden;
        }

        /* Calendar Search & Filter Styles */
        #calendar-search {
            width: 100%;
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 8px;
            border: 1px solid #333;
            background: rgba(0,0,0,0.2);
            color: var(--text-main);
            font-family: var(--font-family);
            box-sizing: border-box;
            outline: none;
            flex-shrink: 0;
        }
        #calendar-search:focus {
            border-color: var(--accent-yellow);
        }

        #calendar-container { flex-grow: 1; overflow-y: auto; padding-right: 5px; }
        .agenda-day { margin-bottom: 20px; }
        .agenda-day h3 { margin: 0 0 10px 0; font-size: 1.2rem; color: var(--text-muted); text-transform: uppercase; }
        
        .event { 
            display: flex; margin-bottom: 15px; background: rgba(255,255,255,0.03); 
            padding: 12px; border-radius: 8px; border-left: 4px solid var(--accent-yellow); 
        }
        .event.kristi { border-left-color: var(--accent-kristi); }
        .event.jake { border-left-color: var(--accent-jake); }
        .event.ryan { border-left-color: var(--accent-ryan); }
        
        .event-time { min-width: 80px; font-weight: bold; color: var(--text-muted); }
        .event-title { flex-grow: 1; display: flex; align-items: center; gap: 6px; }
        
        .owner-badge { font-size: 0.8rem; font-weight: bold; }
        .badge-kristi { color: var(--accent-kristi); }
        .badge-jake { color: var(--accent-jake); }
        .badge-ryan { color: var(--accent-ryan); }

        /* BDT Container Styles */
        #bdt-container {
            margin-top: 15px;
            padding-top: 10px;
            border-top: 1px solid #333;
            font-size: 0.8rem;
            color: var(--text-muted);
            max-height: 120px;
            overflow-y: auto;
            flex-shrink: 0;
        }
        .bdt-item { margin-bottom: 5px; }
        .bdt-item strong { color: #ccc; }

        /* Weather Styles */
        .weather-current { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; flex-shrink: 0;}
        .weather-main { display: flex; flex-direction: column; }
        .weather-temp { font-size: 3.5rem; font-weight: bold; line-height: 1;}
        .weather-cond { font-size: 1.2rem; color: var(--accent-yellow); margin-top: 5px; font-weight: bold;}
        
        .weather-stats {
            background: rgba(255,255,255,0.03); padding: 15px; border-radius: 8px;
            display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 25px; font-size: 1rem; flex-shrink: 0;
        }
        .stat-item { display: flex; justify-content: space-between; }
        .stat-label { color: var(--text-muted); }
        .stat-value { font-weight: bold; }

        .forecast-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; text-align: center; margin-bottom: 15px; flex-shrink: 0;}
        
        .forecast-day {
            background: rgba(255,255,255,0.05); padding: 15px 5px; border-radius: 8px; cursor: pointer;
            transition: all 0.2s ease; border: 1px solid transparent;
        }
        .forecast-day:hover, .forecast-day:active { background: rgba(255,255,255,0.1); }
        .forecast-day.active { border-color: var(--accent-yellow); background: rgba(255, 197, 47, 0.1); }
        .forecast-day .day-name { font-weight: bold; margin-bottom: 5px; font-size: 1.1rem;}
        .forecast-day .day-icon { font-size: 1.5rem; margin: 5px 0;}
        .forecast-day .day-temps { color: var(--text-muted); font-size: 0.9rem; margin-top: 5px; }

        .weather-detail-pane {
            background: var(--bg-color); border-radius: 8px; padding: 15px; margin-top: auto; border-left: 4px solid var(--accent-blue);
        }
        .detail-header { font-weight: bold; margin-bottom: 10px; color: var(--accent-yellow); font-size: 1.1rem;}
        .detail-text { color: var(--text-muted); line-height: 1.4; font-size: 0.95rem;}
        .detail-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 10px; font-size: 0.9rem; }

        /* Grocery List Styles */
        .grocery-list {
            margin: 0; padding: 0; list-style: none; overflow-y: auto; flex-grow: 1; padding-right: 5px;
        }
        .grocery-item {
            padding: 12px 0; border-bottom: 1px solid #333; display: flex; align-items: center; gap: 12px;
        }
        .grocery-item::before {
            content: '⬜'; font-size: 1.1rem; color: var(--text-muted);
        }

        /* Sports Styles */
        .sports-section { margin-bottom: 20px; flex-shrink: 0;}
        .sports-section h3 { margin: 0 0 10px 0; font-size: 1.1rem; color: var(--text-muted); }
        
        .box-score-container {
            max-height: 250px; overflow-y: auto; background: rgba(0,0,0,0.2); border-radius: 8px;
            padding: 10px; margin-bottom: 15px; border: 1px solid #333;
        }
        .schedule-container {
            max-height: 180px; overflow-y: auto; background: rgba(0,0,0,0.2); border-radius: 8px;
            padding: 10px; margin-bottom: 15px; border: 1px solid #333;
        }
        .box-score-header {
            font-size: 0.9rem; color: var(--accent-yellow); border-bottom: 1px solid #444;
            padding-bottom: 5px; margin-bottom: 8px; margin-top: 10px; font-weight: bold;
        }
        .box-score-header:first-child { margin-top: 0; }
        
        .stats-table, .standings-table { width: 100%; border-collapse: collapse; font-size: 0.85rem; }
        .stats-table th, .stats-table td, .standings-table th, .standings-table td { text-align: left; padding: 6px; border-bottom: 1px solid #333; }
        .stats-table th, .standings-table th { color: var(--text-muted); font-weight: normal; }
        .stats-table td:not(:first-child), .stats-table th:not(:first-child) { text-align: right; }
        .schedule-table th, .schedule-table td { text-align: left !important; }
        .standings-table tr.highlight { background: rgba(255, 197, 47, 0.1); font-weight: bold; color: var(--accent-yellow); }

    </style>
</head>
<body>

    <div class="dashboard-container">
        <div class="header">
            <div>
                <div class="date" id="current-date">Loading date...</div>
                <div style="color: var(--text-muted); margin-top: 5px;">📍 Janesville, WI</div>
            </div>
            <div class="time" id="current-time">--:--</div>
        </div>

        <div class="panel">
            <h2>📅 Family Calendar</h2>
            <input type="text" id="calendar-search" placeholder="Search upcoming events..." autocomplete="off">
            <div id="calendar-container">
                <p style="color: var(--text-muted);">Fetching events...</p>
            </div>
            <div id="bdt-container">
                </div>
        </div>

        <div class="col-wrapper">
            <div class="panel" style="flex: 1.2;">
                <h2>☁️ Weather Forecast</h2>
                <div class="weather-current">
                    <div class="weather-main">
                        <div class="weather-temp" id="current-temp">--°</div>
                        <div class="weather-cond" id="current-cond">Loading...</div>
                    </div>
                    <div style="font-size: 4.5rem; line-height: 1;" id="current-icon">☁️</div>
                </div>

                <div class="weather-stats">
                    <div class="stat-item"><span class="stat-label">High/Low:</span> <span class="stat-value" id="current-high-low">--°/--°</span></div>
                    <div class="stat-item"><span class="stat-label">Wind:</span> <span class="stat-value" id="current-wind">-- mph</span></div>
                    <div class="stat-item"><span class="stat-label">Rain Chance:</span> <span class="stat-value" id="current-rain">--%</span></div>
                    <div class="stat-item"><span class="stat-label">Humidity:</span> <span class="stat-value" id="current-humidity">--%</span></div>
                </div>
                
                <h3 style="color: var(--text-muted); font-size: 1.1rem; margin-bottom: 15px;">Forecast (Tap for Details)</h3>
                <div class="forecast-grid" id="forecast-buttons"></div>
                <div class="weather-detail-pane" id="weather-detail-pane">
                    <div class="detail-header">Select a day</div>
                    <div class="detail-text">Tap a day above to view detailed forecast information.</div>
                </div>
            </div>

            <div class="panel" style="flex: 0.8;">
                <h2>🛒 Groceries</h2>
                <ul class="grocery-list" id="grocery-list-container">
                    <li class="grocery-item" style="color: var(--text-muted); border: none;">Fetching list...</li>
                </ul>
            </div>
        </div>

        <div class="panel">
            <h2>⚾ Milwaukee Brewers</h2>
            <div class="sports-section">
                <h3 id="last-game-header">Loading Last Game...</h3>
                <div class="box-score-container">
                    <div class="box-score-header">Brewers Batting</div>
                    <table class="stats-table" id="brewers-batting-table">
                        <tr><th>Player</th><th>AB</th><th>R</th><th>H</th><th>RBI</th></tr>
                        <tr><td colspan="5" style="text-align:center;">Loading stats...</td></tr>
                    </table>

                    <div class="box-score-header">Brewers Pitching</div>
                    <table class="stats-table" id="brewers-pitching-table">
                        <tr><th>Player</th><th>IP</th><th>H</th><th>ER</th><th>BB</th><th>K</th></tr>
                        <tr><td colspan="6" style="text-align:center;">Loading stats...</td></tr>
                    </table>
                </div>
            </div>

            <div class="sports-section">
                <h3>Upcoming Schedule (7 Days)</h3>
                <div class="schedule-container" id="upcoming-schedule-container">
                    <table class="stats-table schedule-table">
                        <tr><td style="text-align:center;">Loading schedule...</td></tr>
                    </table>
                </div>
            </div>

            <div class="sports-section" style="margin-top: auto;">
                <h3>NL Central Standings</h3>
                <table class="standings-table" id="nl-central-table">
                    <tr><th>Team</th><th>W</th><th>L</th><th>GB</th></tr>
                    <tr><td colspan="4" style="text-align:center;">Loading standings...</td></tr>
                </table>
            </div>
        </div>
    </div>

    <script>
        // ==========================================
        // CONFIGURATION
        // ==========================================
        const GOOGLE_APP_SCRIPT_URL = '
https://script.google.com/macros/s/AKfycbxaeukSONHtGu0vKoDQsEYRcHLV3dmdeFrXfeL6mmuCMmuEM-LtFs56_WBgHnJnmX4X/exec
'; // Paste your script URL here

        // --- 1. Live Clock Functionality ---
        function updateClock() {
            const now = new Date();
            let hours = now.getHours();
            let minutes = now.getMinutes();
            const ampm = hours >= 12 ? 'PM' : 'AM';
            hours = hours % 12 || 12; 
            minutes = minutes < 10 ? '0' + minutes : minutes;
            document.getElementById('current-time').textContent = `${hours}:${minutes} ${ampm}`;
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            document.getElementById('current-date').textContent = now.toLocaleDateString('en-US', options);
        }
        setInterval(updateClock, 1000);
        updateClock();

        // --- 2. Live Calendar & Groceries (Via Google Apps Script) ---
        let globalCalendarEvents = []; // Store events globally so we can search them without refetching

        async function fetchGoogleData() {
            const calContainer = document.getElementById('calendar-container');
            const grocContainer = document.getElementById('grocery-list-container');
            
            if (GOOGLE_APP_SCRIPT_URL === 'YOUR_WEB_APP_URL_HERE') {
                calContainer.innerHTML = `<p style="color: var(--accent-yellow); background: rgba(255, 197, 47, 0.1); padding: 15px; border-radius: 8px;">
                                       ⚠️ <b>Setup Required</b><br>Paste your Google Apps Script URL.</p>`;
                return;
            }

            try {
                const urlWithCacheBuster = GOOGLE_APP_SCRIPT_URL + "?t=" + new Date().getTime();
                const res = await fetch(urlWithCacheBuster);
                const data = await res.json();
                
                // Store events globally and trigger render functions
                globalCalendarEvents = data.events;
                renderCalendar();
                renderBDTList();

                // Render Groceries
                const groceries = data.groceries;
                if (!groceries || groceries.length === 0) {
                    grocContainer.innerHTML = `<li class="grocery-item" style="color: var(--text-muted); border: none;">List is empty.</li>`;
                } else {
                    grocContainer.innerHTML = groceries.map(item => `<li class="grocery-item">${item}</li>`).join('');
                }

            } catch (error) {
                console.error("Google Data fetch error:", error);
            }
        }

        function renderCalendar() {
            const container = document.getElementById('calendar-container');
            const searchQuery = document.getElementById('calendar-search').value.toLowerCase();
            
            let filteredEvents = globalCalendarEvents;
            if (searchQuery) {
                filteredEvents = globalCalendarEvents.filter(evt => 
                    evt.title.toLowerCase().includes(searchQuery) || 
                    (evt.description && evt.description.toLowerCase().includes(searchQuery))
                );
            }

            if (filteredEvents.length === 0) {
                container.innerHTML = `<p style="color: var(--text-muted); font-style: italic;">No matching events found.</p>`;
                return;
            }

            const groupedEvents = {};
            filteredEvents.forEach(evt => {
                const eventDate = new Date(evt.time);
                const dateStr = eventDate.toDateString();
                if (!groupedEvents[dateStr]) groupedEvents[dateStr] = [];
                groupedEvents[dateStr].push(evt);
            });

            const formatTime = (dateString, isAllDay) => {
                if (isAllDay) return "All Day";
                const d = new Date(dateString);
                return d.toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' });
            };

            const getDayLabel = (dateString) => {
                const d = new Date(dateString);
                const today = new Date();
                const tomorrow = new Date(); tomorrow.setDate(today.getDate() + 1);
                
                if (d.toDateString() === today.toDateString()) return "Today";
                if (d.toDateString() === tomorrow.toDateString()) return "Tomorrow";
                return d.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
            };

            let htmlString = "";
            const sortedDates = Object.keys(groupedEvents).sort((a, b) => new Date(a) - new Date(b));
            
            sortedDates.forEach(dateStr => {
                htmlString += `<div class="agenda-day"><h3>${getDayLabel(dateStr)}</h3>`;
                
                groupedEvents[dateStr].forEach(evt => {
                    let eventClass = 'event';
                    let badge = '';
                    
                    if (evt.owner !== 'Dave') {
                        const ownerLower = evt.owner.toLowerCase();
                        const initial = evt.owner.charAt(0).toUpperCase();
                        eventClass = `event ${ownerLower}`;
                        badge = `<span class="owner-badge badge-${ownerLower}">(${initial})</span>`;
                    }
                    
                    htmlString += `
                        <div class="${eventClass}">
                            <div class="event-time">${formatTime(evt.time, evt.isAllDay)}</div>
                            <div class="event-title">${badge}${evt.title}</div>
                        </div>`;
                });
                htmlString += `</div>`;
            });

            container.innerHTML = htmlString;
        }

        function renderBDTList() {
            const bdtContainer = document.getElementById('bdt-container');
            
            // Filter global events where description contains "BDT"
            const bdtEvents = globalCalendarEvents.filter(evt => evt.description && evt.description.includes('BDT'));
            
            if (bdtEvents.length === 0) {
                bdtContainer.innerHTML = `<div style="font-style: italic;">No BDT notes found in upcoming events.</div>`;
                return;
            }
            
            let html = `<div style="margin-bottom: 8px; font-weight: bold; color: var(--accent-yellow); text-transform: uppercase;">Upcoming BDT Notes:</div>`;
            bdtEvents.forEach(evt => {
                const eventDate = new Date(evt.time);
                const dateStr = eventDate.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
                html += `<div class="bdt-item"><strong>${evt.title}</strong> - ${dateStr}</div>`;
            });
            
            bdtContainer.innerHTML = html;
        }

        // Add event listener to search bar to filter in real-time
        document.getElementById('calendar-search').addEventListener('input', renderCalendar);

        fetchGoogleData();
        setInterval(fetchGoogleData, 900000); // 15 mins

        // --- 3. Live Weather (Open-Meteo API) ---
        const getWeatherInfo = (code) => {
            if (code === 0) return { desc: 'Clear sky', icon: '☀️' };
            if ([1, 2, 3].includes(code)) return { desc: 'Partly cloudy', icon: '⛅' };
            if ([45, 48].includes(code)) return { desc: 'Fog', icon: '🌫️' };
            if ([51, 53, 55, 56, 57].includes(code)) return { desc: 'Drizzle', icon: '🌧️' };
            if ([61, 63, 65, 66, 67].includes(code)) return { desc: 'Rain', icon: '🌧️' };
            if ([71, 73, 75, 77, 85, 86].includes(code)) return { desc: 'Snow', icon: '❄️' };
            if ([80, 81, 82].includes(code)) return { desc: 'Rain showers', icon: '🌦️' };
            if ([95, 96, 99].includes(code)) return { desc: 'Thunderstorm', icon: '⛈️' };
            return { desc: 'Unknown', icon: '☁️' };
        };

        let storedForecastData = {};

        async function fetchWeather() {
            const lat = 42.6828;
            const lon = -89.0187;
            const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_probability_max,wind_speed_10m_max&temperature_unit=fahrenheit&wind_speed_unit=mph&precipitation_unit=inch&timezone=America%2FChicago`;

            try {
                const res = await fetch(url);
                const data = await res.json();
                const current = data.current;
                const daily = data.daily;
                const info = getWeatherInfo(current.weather_code);
                
                document.getElementById('current-temp').textContent = `${Math.round(current.temperature_2m)}°`;
                document.getElementById('current-cond').textContent = info.desc;
                document.getElementById('current-icon').textContent = info.icon;
                document.getElementById('current-high-low').textContent = `${Math.round(daily.temperature_2m_max[0])}° / ${Math.round(daily.temperature_2m_min[0])}°`;
                document.getElementById('current-wind').textContent = `${Math.round(current.wind_speed_10m)} mph`;
                document.getElementById('current-rain').textContent = `${daily.precipitation_probability_max[0]}%`;
                document.getElementById('current-humidity').textContent = `${current.relative_humidity_2m}%`;

                const buttonsContainer = document.getElementById('forecast-buttons');
                buttonsContainer.innerHTML = '';
                storedForecastData = {};
                const daysOfWeek = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];

                for (let i = 0; i < 4; i++) {
                    const dateObj = new Date(daily.time[i] + 'T00:00:00');
                    const dayName = i === 0 ? 'Today' : daysOfWeek[dateObj.getDay()];
                    const dayInfo = getWeatherInfo(daily.weather_code[i]);
                    const high = Math.round(daily.temperature_2m_max[i]);
                    const low = Math.round(daily.temperature_2m_min[i]);
                    const rain = daily.precipitation_probability_max[i];
                    const wind = Math.round(daily.wind_speed_10m_max[i]);

                    const dayId = `day-${i}`;
                    storedForecastData[dayId] = {
                        title: `${dayName} Forecast`,
                        desc: `Expect ${dayInfo.desc.toLowerCase()} with a high of ${high}° and a low of ${low}°.`,
                        rain: `${rain}%`,
                        wind: `${wind} mph`
                    };

                    const btn = document.createElement('div');
                    btn.className = `forecast-day ${i === 0 ? 'active' : ''}`;
                    btn.onclick = function() { updateWeatherDetail(dayId, this); };
                    btn.innerHTML = `<div class="day-name">${dayName}</div><div class="day-icon">${dayInfo.icon}</div><div class="day-temps">${high}° / ${low}°</div>`;
                    buttonsContainer.appendChild(btn);

                    if (i === 0) updateWeatherDetail(dayId, btn);
                }
            } catch (error) { console.error("Weather fetch failed", error); }
        }

        function updateWeatherDetail(dayId, element) {
            document.querySelectorAll('.forecast-day').forEach(btn => btn.classList.remove('active'));
            if(element) element.classList.add('active');
            const data = storedForecastData[dayId];
            if(!data) return;
            document.getElementById('weather-detail-pane').innerHTML = `
                <div class="detail-header">${data.title}</div><div class="detail-text">${data.desc}</div>
                <div class="detail-grid"><div><span style="color: var(--text-muted);">Rain:</span> ${data.rain}</div><div><span style="color: var(--text-muted);">Max Wind:</span> ${data.wind}</div></div>`;
        }

        fetchWeather();
        setInterval(fetchWeather, 1800000); // 30 mins

        // --- 4. Live MLB Data Fetch ---
        async function fetchBrewersData() {
            const teamId = 158; 
            try {
                const today = new Date();
                const pastDate = new Date(today); pastDate.setDate(today.getDate() - 7);
                const futureDate = new Date(today); futureDate.setDate(today.getDate() + 7);
                const formatDate = (d) => `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}-${String(d.getDate()).padStart(2, '0')}`;

                const schedUrl = `https://statsapi.mlb.com/api/v1/schedule?sportId=1&teamId=${teamId}&startDate=${formatDate(pastDate)}&endDate=${formatDate(futureDate)}&hydrate=probablePitcher(note),team`;
                const schedRes = await fetch(schedUrl);
                const schedData = await schedRes.json();

                let lastGame = null;
                let upcomingGames = [];

                if (schedData.dates) {
                    const allGames = schedData.dates.flatMap(d => d.games);
                    const completedGames = allGames.filter(g => g.status.statusCode === 'F' || g.status.statusCode === 'C');
                    upcomingGames = allGames.filter(g => g.status.statusCode === 'S' || g.status.statusCode === 'P' || g.status.statusCode === 'I');
                    if (completedGames.length > 0) lastGame = completedGames[completedGames.length - 1];
                }

                // A. Process Last Game & Box Score
                if (lastGame) {
                    const isHome = lastGame.teams.home.team.id === teamId;
                    const oppAbbrev = isHome ? lastGame.teams.away.team.abbreviation : lastGame.teams.home.team.abbreviation;
                    const homeScore = lastGame.teams.home.score;
                    const awayScore = lastGame.teams.away.score;
                    const homeAbbr = isHome ? 'MIL' : oppAbbrev;
                    const awayAbbr = isHome ? oppAbbrev : 'MIL';
                    
                    document.getElementById('last-game-header').innerHTML = `Last Game: ${awayAbbr} ${awayScore} - ${homeAbbr} ${homeScore} <span style="font-size: 0.8em; font-weight: normal; color: #ccc;">(Final)</span>`;

                    const boxUrl = `https://statsapi.mlb.com/api/v1/game/${lastGame.gamePk}/boxscore`;
                    const boxRes = await fetch(boxUrl);
                    const boxData = await boxRes.json();
                    const brewersBox = isHome ? boxData.teams.home : boxData.teams.away;

                    let battingHtml = `<tr><th>Player</th><th>AB</th><th>R</th><th>H</th><th>RBI</th></tr>`;
                    brewersBox.batters.forEach(id => {
                        const player = brewersBox.players[`ID${id}`];
                        if (player.stats && player.stats.batting && player.stats.batting.atBats > 0) {
                            battingHtml += `<tr><td>${player.person.boxscoreName}, ${player.position.abbreviation}</td><td>${player.stats.batting.atBats}</td><td>${player.stats.batting.runs}</td><td>${player.stats.batting.hits}</td><td>${player.stats.batting.rbi}</td></tr>`;
                        }
                    });
                    document.getElementById('brewers-batting-table').innerHTML = battingHtml;

                    let pitchingHtml = `<tr><th>Player</th><th>IP</th><th>H</th><th>ER</th><th>BB</th><th>K</th></tr>`;
                    brewersBox.pitchers.forEach(id => {
                        const player = brewersBox.players[`ID${id}`];
                        if (player.stats && player.stats.pitching) {
                            let note = '';
                            if (player.gameStatus) {
                                if (player.gameStatus.isWinningPitcher) note = ' (W)';
                                if (player.gameStatus.isLosingPitcher) note = ' (L)';
                                if (player.gameStatus.isSavePitcher) note = ' (S)';
                            }
                            pitchingHtml += `<tr><td>${player.person.boxscoreName}${note}</td><td>${player.stats.pitching.inningsPitched}</td><td>${player.stats.pitching.hits}</td><td>${player.stats.pitching.earnedRuns}</td><td>${player.stats.pitching.baseOnBalls}</td><td>${player.stats.pitching.strikeOuts}</td></tr>`;
                        }
                    });
                    document.getElementById('brewers-pitching-table').innerHTML = pitchingHtml;
                }

                // B. Process Upcoming Schedule (7 Days)
                let scheduleHtml = `<table class="stats-table schedule-table">
                    <tr><th>Date</th><th>Opponent</th><th>Time</th><th>Probable Pitchers</th></tr>`;
                
                if (upcomingGames.length === 0) {
                    scheduleHtml += `<tr><td colspan="4" style="text-align:center; padding: 15px;">No games scheduled in the next 7 days.</td></tr>`;
                } else {
                    upcomingGames.forEach(game => {
                        const isHome = game.teams.home.team.id === teamId;
                        const oppAbbr = isHome ? game.teams.away.team.abbreviation : game.teams.home.team.abbreviation;
                        const vsAt = isHome ? 'vs' : '@';
                        const gameDate = new Date(game.gameDate);
                        
                        const timeString = gameDate.toLocaleTimeString([], {hour: 'numeric', minute:'2-digit'});
                        const dateString = gameDate.toLocaleDateString([], {weekday: 'short', month: 'short', day: 'numeric'});

                        const getLastName = (fullName) => fullName ? fullName.split(' ').pop() : 'TBD';
                        const awayP = game.teams.away.probablePitcher ? getLastName(game.teams.away.probablePitcher.fullName) : 'TBD';
                        const homeP = game.teams.home.probablePitcher ? getLastName(game.teams.home.probablePitcher.fullName) : 'TBD';

                        scheduleHtml += `<tr>
                            <td style="white-space:nowrap;">${dateString}</td>
                            <td>${vsAt} ${oppAbbr}</td>
                            <td style="white-space:nowrap;">${timeString}</td>
                            <td><span style="color: #ccc; font-size: 0.85rem;">${awayP} @ ${homeP}</span></td>
                        </tr>`;
                    });
                }
                scheduleHtml += `</table>`;
                document.getElementById('upcoming-schedule-container').innerHTML = scheduleHtml;

                // C. Process Standings
                const currentYear = today.getFullYear();
                const stdUrl = `https://statsapi.mlb.com/api/v1/standings?leagueId=104&season=${currentYear}`;
                const stdRes = await fetch(stdUrl);
                const stdData = await stdRes.json();
                
                const nlCentral = stdData.records.find(r => r.division.id === 205);
                if (nlCentral) {
                    let stdHtml = `<tr><th>Team</th><th>W</th><th>L</th><th>GB</th></tr>`;
                    nlCentral.teamRecords.forEach(team => {
                        const isBrewers = team.team.id === teamId;
                        const rowClass = isBrewers ? 'class="highlight"' : '';
                        stdHtml += `<tr ${rowClass}><td>${team.team.name}</td><td>${team.wins}</td><td>${team.losses}</td><td>${team.gamesBack}</td></tr>`;
                    });
                    document.getElementById('nl-central-table').innerHTML = stdHtml;
                }
            } catch (error) { console.error("MLB fetch error:", error); }
        }
        fetchBrewersData();
        setInterval(fetchBrewersData, 1800000); // 30 mins

    </script>
</body>
</html>
