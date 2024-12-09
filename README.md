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
            font-family: Arial, sans-serif;
        }
        #map {
            height: 80vh; /* 地圖佔據螢幕 80% 高度 */
            width: 100%;  /* 地圖寬度 100% */
        }
        .description {
            padding: 20px;
            background-color: #f9f9f9;
            text-align: center;
            font-size: 18px;
            color: #333;
            margin: 20px 0;
        }
        .footer {
            text-align: center;
            padding: 15px;
            font-size: 16px;
            background-color: #f9f9f9;
            margin-top: 20px;
        }
        a {
            color: #007bff;
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
        img {
            max-width: 100%;
            height: auto;
            margin-top: 20px;
        }
    </style>
</head>
<body>

    <!-- 說明文字區域 -->
    <div class="description">
        <p><strong>土壤液化</strong> 是因為「砂質土壤」結合「高地下水位」的狀況，遇到一定強度的地震搖晃，導致類似砂質顆粒浮在水中的現象，因而使砂質土壤失去承載建築物重量的力量，造成建築物下陷或傾斜。</p>
        <!-- 添加圖片 -->
        <img src="https://www.liquid.net.tw/cgs/public/images/QA/8-1.png" alt="土壤液化圖片">
    </div>

    <!-- 地圖容器 -->
    <div id="map"></div>

    <!-- 查詢連結區域 -->
    <div class="footer">
        <p>查詢更多土壤液化潛勢資料，請參考 <a href="https://www.liquid.net.tw/cgs/public/" target="_blank">土壤液化潛勢查詢系統</a></p>
    </div>

    <script>
        // 初始化地圖
        const map = L.map('map').setView([24.147736, 120.673648], 12); // 台中市中心座標

        // 添加 OpenStreetMap 圖層
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // 定義資料來源及顏色對應
        const datasets = [
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E9%AB%98%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "red",    // 高潛勢：紅色
                fillColor: "red"
            },
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E4%B8%AD%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "orange", // 中潛勢：橘色
                fillColor: "orange"
            },
            {
                url: "https://www.geologycloud.tw/api/v1/zh-tw/liquefaction?area=%E8%87%BA%E4%B8%AD&classify=%E4%BD%8E%E6%BD%9B%E5%8B%A2&all=true&t=.json",
                color: "#FFFF99", // 低潛勢：亮黃色
                fillColor: "#FFFF99"
            }
        ];

        // 獲取並繪製每個資料集
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
                                <strong>描述：</strong>${properties.description || "紅-高度潛勢區；橘-中度潛勢區；黃-低度潛勢區 "}
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
