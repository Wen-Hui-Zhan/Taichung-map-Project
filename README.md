# taichung-map-project
台中土壤液化潛勢區


<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>台中市土壤液化潛勢地圖</title>
    <!-- 引入 Leaflet.js -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        #map {
            height: 100vh; /* 地圖全螢幕高度 */
            width: 100%;  /* 地圖全螢幕寬度 */
        }
    </style>
</head>
<body>
    <div id="map"></div>
    <script>
        
        const map = L.map('map').setView([24.147736, 120.673648], 12);

        // 添加 OpenStreetMap 圖層
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // 資料來源、顏色
        const datasets = [
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E9%AB%98%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "red",    // 高潛勢(紅色)
                fillColor: "red"
            },
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E4%B8%AD%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "orange", // 中潛勢(橘色)
                fillColor: "orange"
            },
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E4%BD%8E%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "yellow", // 低潛勢(黃色)
                fillColor: "yellow"
            }
        ];

        datasets.forEach(dataset => {
            fetch(dataset.url)
                .then(response => response.json())
                .then(data => {
                    // 將 GeoJSON 資料加入地圖
                    L.geoJSON(data, {
                        style: {
                            color: dataset.color,
                            fillColor: dataset.fillColor,
                            fillOpacity: 0.5,
                            weight: 1
                        },
                        onEachFeature: (feature, layer) => {
                            // 點擊每個多邊形顯示詳細資訊
                            const properties = feature.properties;
                            const popupContent = `
                                <strong>土壤液化潛勢：</strong>${properties.classify || "未知"}<br>
                                <strong>描述：</strong>${properties.description || "無詳細描述"}
                            `;
                            layer.bindPopup(popupContent);
                        }
                    }).addTo(map);
                })
                .catch(error => console.error(`資料載入失敗 (${dataset.url}):`, error));
        });
    </script>
</body>
</html>
