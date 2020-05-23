<template>
  <div class="mapContainer">
    <div class="title">
      <h2>台中市區屋齡地圖</h2>
      <p>視覺化舊台中市建物落成年份</p>
      <span class="source">
        資料來源：<a
          target="_blank"
          href="https://lohas.taichung.gov.tw/webgis/index.html#"
          >台中地政局</a
        >
      </span>
      <span
        >製作：<a href="https://linchiahsien.netlify.app/" target="_blank"
          >林佳賢</a
        ><a href="https://twitter.com/imandylin2" target="_blank"
          ><img src="@/assets/image/Twitter_Social_Icon_Circle_Color.png"
        /></a>
        <a
          href="https://github.com/imdataman/taichung-building-age-map"
          target="_blank"
          ><img src="@/assets/image/GitHub-Mark-32px.png"
        /></a>
      </span>
    </div>
    <div id="map"></div>
    <div
      class="buildingInfo"
      v-if="
        Object.entries(buildingInfo).length !== 0 &&
          buildingInfo.constructor === Object
      "
    >
      <div>落成年份: {{ buildingInfo.age }}</div>
      <div>樓層數: {{ buildingInfo.建物樓層數 }}</div>
      <div>型態碼: {{ buildingInfo.房屋型態碼 }}</div>
      <div>結構碼: {{ buildingInfo.房屋結構碼 }}</div>
    </div>
    <div class="legend">
      <div class="colorWrapper">
        <div
          class="color"
          v-for="p in legend.palette"
          :key="p"
          :style="{ 'background-color': p }"
        ></div>
      </div>
      <div class="textWrapper">
        <span class="text" v-for="b in breaks" :key="b">
          {{ b }}
        </span>
      </div>
    </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
const Buffer = require("buffer").Buffer;
const wkx = require("wkx");

export default {
  name: "Map",
  data() {
    return {
      mapStyle: {
        version: 8,
        name: "Blank",
        sources: {
          "raster-tiles": {
            type: "raster",
            tiles: ["tile/{z}/{x}/{y}.png"],
            tileSize: 256
          }
        },
        layers: [
          {
            id: "simple-tiles",
            type: "raster",
            source: "raster-tiles"
          }
        ]
      },
      center: [120.67, 24.15],
      radius: 0.5,
      legend: {
        breaks: [2, 61, 71, 76, 81, 86, 91, 96, 108],
        palette: [
          "#FDE725",
          "#9FDA3A",
          "#4AC16D",
          "#1FA187",
          "#277F8E",
          "#365C8D",
          "#46337E",
          "#440154"
        ]
      },
      buildingInfo: {}
    };
  },
  computed: {
    breaks() {
      const slicedBreaks = this.legend.breaks.slice(0, 8);
      return slicedBreaks.map((d, i) =>
        i === 0
          ? `民國${d} - ${this.legend.breaks[i + 1] - 1}年`
          : `${d} - ${this.legend.breaks[i + 1] - 1}`
      );
    }
  },
  mounted() {
    const map = new mapboxgl.Map({
      container: "map",
      style: this.mapStyle,
      center: this.center,
      zoom: 13,
      maxZoom: 15,
      minZoom: 12
    });
    // disable map rotation using right click + drag
    map.dragRotate.disable();

    // disable map rotation using touch rotation gesture
    map.touchZoomRotate.disableRotation();

    map.on("load", function() {
      map.addSource("polygon", {
        type: "geojson",
        data: {
          type: "FeatureCollection",
          features: []
        }
      });
      map.addLayer({
        id: "selectedPolygon",
        type: "line",
        source: "polygon",
        paint: {
          "line-color": "black",
          "line-width": 2
        }
      });
    });

    map.on("click", e => {
      fetch(
        `https://76kkbnkxk9.execute-api.ap-southeast-1.amazonaws.com/dev/polygon/${e.lngLat.lng}/${e.lngLat.lat}`,
        {}
      )
        .then(response => {
          return response.json();
        })
        .then(jsonData => {
          if (jsonData[0]) {
            const wkbBuffer = new Buffer(jsonData[0].geom, "hex");
            const geometry = wkx.Geometry.parse(wkbBuffer).toGeoJSON();
            this.buildingInfo = jsonData[0];
            map.getSource("polygon").setData(geometry);
          } else {
            this.buildingInfo = {};
            map.getSource("polygon").setData({
              type: "FeatureCollection",
              features: []
            });
          }
        })
        .catch(err => {
          console.log("錯誤:", err);
        });
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.title {
  position: absolute;
  top: 0;
  left: 0;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 1rem;
  h2,
  p,
  span {
    margin: 10px;
  }
}
a {
  color: black;
  &:hover {
    color: steelblue;
  }
}
span {
  font-size: 0.75rem;
  img {
    height: 1rem;
    padding-left: 0.25rem;
    vertical-align: sub;
  }
}
.source {
  padding-right: 0.5rem;
}
#map {
  width: 100vw;
  height: 100vh;
  cursor: pointer;
}
.mapContainer {
  position: relative;
}
.legend {
  position: absolute;
  right: 30px;
  bottom: 20%;
  display: flex;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 0px 2.5px;
}
.colorWrapper,
.textWrapper {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  padding: 5px 2px;
}
.buildingInfo {
  position: absolute;
  right: 30px;
  bottom: 45%;
  display: flex;
  flex-direction: column;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 5px;
  font-size: 1.25rem;
}
.color {
  width: 1rem;
  height: 1rem;
  border-radius: 2px;
}
.text {
  font-size: 1rem;
}
@media only screen and (max-width: 480px) {
  .text {
    font-size: 0.75rem;
  }
  .title {
    h2,
    p,
    span {
      margin: 0px;
    }
  }
  .legend,
  .buildingInfo {
    right: 5px;
  }
  .buildingInfo {
    font-size: 1rem;
  }
  .color {
    border-radius: 0px;
  }
}
</style>
