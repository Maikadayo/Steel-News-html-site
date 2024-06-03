<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="utf-8">
  <title>Steel News Satellite</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- Include the Mapbox GL CSS -->
  <link href='https://api.mapbox.com/mapbox-gl-js/v3.3.0/mapbox-gl.css' rel='stylesheet' />
  <style>
    body, html {
      height: 100%;
      margin: 0;
      padding: 0;
      font-family: 'Arial', sans-serif;
    }
    #header {
      width: 100%;
      padding: 10px 0;
      background: #333;
      color: #fff;
      text-align: center;
      font-size: 24px;
    }
    #container {
      display: flex;
      height: calc(100% - 50px); /* Adjusted for header height */
      overflow: hidden;
      position: relative;
    }
    #news-list {
      position: absolute;
      left: -100%;
      width: 100%;
      height: 100%;
      padding: 20px;
      box-sizing: border-box;
      overflow-y: auto;
      background-color: rgba(255, 255, 255, 0.9);
      transition: left 0.3s ease;
      z-index: 2;
    }
    #map {
      flex: 1;
      height: 100%;
      z-index: 1;
    }
    .news-item {
      margin-bottom: 10px;
      padding: 10px;
      border: 1px solid #ddd;
      background-color: #fff;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
    .news-item:hover {
      background-color: #eee;
    }
    .menu-button {
      position: absolute;
      top: 10px;
      left: 10px;
      padding: 10px 20px;
      background: #333;
      color: #fff;
      cursor: pointer;
      z-index: 3;
      border: none;
      border-radius: 5px;
    }
    .close-button {
      position: absolute;
      top: 10px;
      right: 10px;
      padding: 10px;
      background: #333;
      color: #fff;
      cursor: pointer;
      z-index: 3;
      border: none;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <div id="header">Steel News Satellite</div>
  <button class="menu-button" onclick="toggleMenu()">Menu</button>
  <div id="container">
    <div id="news-list">
      <button class="close-button" onclick="toggleMenu()">Close</button>
      <!-- News list will be dynamically populated here -->
    </div>
    <div id="map"></div>
  </div>
  <!-- Include the Mapbox GL JS -->
  <script src='https://api.mapbox.com/mapbox-gl-js/v3.3.0/mapbox-gl.js'></script>
  <script>
    mapboxgl.accessToken = 'pk.eyJ1IjoiaGFydWNoYW5uIiwiYSI6ImNsdnFlZWRidTAyNmkya21yem1wMjhzZHUifQ.GrEXJlVvv0ljdkQXoEQTTQ';
    const map = new mapboxgl.Map({
      container: 'map', // container ID
      style: 'mapbox://styles/haruchann/clvqqksup01r901qpg3d0hynv', // style URL
      center: [31.261181743597245,30.033253480264698], // initial center coordinates
      zoom: 1.9, // initial zoom
      pitch: 0, // pitch in degrees
      bearing: 0 // bearing in degrees
    });

    map.addControl(new mapboxgl.NavigationControl());
    map.on('style.load', () => {
        map.setFog({}); // Set the default atmosphere style
    });

    const newsArticles = [
      {
        title: "Vietnam targets higher steel output, lower emissions",
        date: "05/13/2024",
        content: "Vietnam could produce 51 million tonnes of crude steel in 2050, Vietnam Steel Association (VSA) secretary general and vice chairman Dinh Quoc Thai said at Monday’s South East Asian Iron and Steel Institute (Seaisi) annual conference.",
        location: [105.8148158587074,21.01580842214596]
      },
      {
        title: "ArcelorMittal Restocking Outlook",
        date: "05/03/2024",
        content: "ArcelorMittal observes a waiting period for a rebound in restocking. Sentiment stabilizes, awaiting consumption increase due to low inventory levels. Maintains global steel demand growth forecast for 2024. Q1 shows declines in production, shipments, net profit, and Ebitda, but revenue grows compared to Q4 2023. European steel spreads improve, US prices stabilize influenced by low inventory. Challenges noted from China’s excess steel production and rising exports. Focus on upcoming projects for low-emission steel supply.",
        location: [6.143465193777926,49.62180385642374]
      }
    ];

    const newsList = document.getElementById('news-list');
    let activeContentDiv = null;

    newsArticles.forEach(article => {
      const newsItem = document.createElement('div');
      newsItem.className = 'news-item';
      newsItem.innerText = article.title;

      const contentDiv = document.createElement('div');
      contentDiv.className = 'news-content';
      contentDiv.innerHTML = `<p>${article.content}</p><span class="detail-link" data-location='${JSON.stringify(article.location)}'>Detail</span>`;
      contentDiv.style.display = 'none';

      newsItem.addEventListener('click', () => {
        map.flyTo({
          center: article.location,
          zoom: 10, // Initial zoom level
          essential: true
        });

        // Hide the previously active content
        if (activeContentDiv && activeContentDiv !== contentDiv) {
          activeContentDiv.style.display = 'none';
        }

        // Toggle content display
        contentDiv.style.display = contentDiv.style.display === 'none' ? 'block' : 'none';
        activeContentDiv = contentDiv;

        const detailLink = contentDiv.querySelector('.detail-link');
        detailLink.addEventListener('click', (e) => {
          e.stopPropagation(); //
