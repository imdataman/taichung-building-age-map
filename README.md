# 台中市區屋齡地圖 Building Age Map of Downtown Taichung

舊台中市八個行政區的建物落成年份地圖 A map application showing the year each building in Downtown Taichung was built

## 研究方法

1. 先從政府開放網站下載[台中市建物輪廓資料](https://data.gov.tw/dataset/81535 "台中市建物輪廓資料")
2. 從台中市政府地政局的[空間資訊系統](https://lohas.taichung.gov.tw/webgis/index.html# "空間資訊系統")，取得台中市區的建築年度分佈點位資料
3. 運用R語言，計算哪些每個建築年度點位落在哪些建物輪廓之中
（請注意，部分建築分佈點位與其所屬的建築輪廓存在定位誤差，因此並非所有建築都有配對到建築年度）
4. 篩掉沒有配對到建築年度的建物輪廓後，剩下的建築根據建物落成年份著色
5. 再添加行政區範圍、行政區名稱和主要道路線條等圖層以輔助閱讀
6. 運用QGIS產出圖磚
7. 使用Mapbox GL JS寫互動地圖

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
