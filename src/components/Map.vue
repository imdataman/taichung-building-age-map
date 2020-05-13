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
// import * as topojson from "topojson-client";
// import buildingRaw from "@/assets/data/building_footprint_with_age.json";
// import taichung from "@/assets/data/taichung.json";
// import taichungLabel from "@/assets/data/taichung_label.json";
// import taichungRoads from "@/assets/data/taichung_roads.json";

// const building = topojson.feature(
//   buildingRaw,
//   buildingRaw.objects.building_footprint_with_age
// );

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
      }
    };
  },
  computed: {
    map() {
      return new mapboxgl.Map({
        container: "map",
        style: this.mapStyle,
        center: this.center,
        zoom: 13,
        maxZoom: 15,
        minZoom: 12
      });
    },
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
    // disable map rotation using right click + drag
    this.map.dragRotate.disable();

    // disable map rotation using touch rotation gesture
    this.map.touchZoomRotate.disableRotation();

    // this.map.on("load", async () => {
    //   this.map.addSource("road", {
    //     type: "geojson",
    //     data: taichungRoads
    //   });

    //   this.map.addLayer({
    //     id: "roadLine",
    //     source: "road",
    //     type: "line",
    //     layout: {
    //       "line-join": "round",
    //       "line-cap": "round"
    //     },
    //     paint: {
    //       "line-color": "#ccc",
    //       "line-width": [
    //         "case",
    //         ["==", ["get", "highway"], "primary"],
    //         1,
    //         ["==", ["get", "highway"], "secondary"],
    //         0.75,
    //         0.5
    //       ]
    //     }
    //   });

    //   this.map.addSource("building", {
    //     type: "geojson",
    //     data: building
    //   });

    //   this.map.addLayer({
    //     id: "buildingPolygon",
    //     type: "fill",
    //     source: "building",
    //     paint: {
    //       "fill-color": [
    //         "step",
    //         ["get", "age"],
    //         this.legend.palette[0],
    //         this.legend.breaks[1],
    //         this.legend.palette[1],
    //         this.legend.breaks[2],
    //         this.legend.palette[2],
    //         this.legend.breaks[3],
    //         this.legend.palette[3],
    //         this.legend.breaks[4],
    //         this.legend.palette[4],
    //         this.legend.breaks[5],
    //         this.legend.palette[5],
    //         this.legend.breaks[6],
    //         this.legend.palette[6],
    //         this.legend.breaks[7],
    //         this.legend.palette[7]
    //       ]
    //     }
    //   });

    //   this.map.addSource("border", {
    //     type: "geojson",
    //     data: taichung
    //   });

    //   this.map.addLayer({
    //     id: "borderLine",
    //     type: "line",
    //     source: "border",
    //     layout: {
    //       "line-join": "round",
    //       "line-cap": "round"
    //     },
    //     paint: {
    //       "line-color": "black",
    //       "line-width": 1
    //     }
    //   });

    //   this.map.addSource("label", {
    //     type: "geojson",
    //     data: taichungLabel
    //   });

    //   this.map.addLayer({
    //     id: "townText",
    //     source: "label",
    //     type: "symbol",
    //     layout: {
    //       "symbol-placement": "point",
    //       "text-field": "{TOWNNAME}",
    //       "text-size": 16
    //     },
    //     paint: {
    //       "text-halo-width": 2,
    //       "text-halo-color": "#fff"
    //     }
    //   });
    // });
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
  .legend {
    right: 5px;
  }
  .color {
    border-radius: 0px;
  }
}
</style>
