<template>
  <div class="main">
    <div class="flex">
      <div class="map-holder">
        <div id="map"></div>
      </div>
    </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import "@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css";
import '@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css';
import API from '../api/API.vue';
import { h, nextTick, render } from "vue";
import MapPopupComponent from './Contextmenu.vue';
const MAP_TOKEN = import.meta.env.VITE_MAP_ACCESS_TOKEN;

export default {
  data() {
    return {
      loading: false,
      location: "",
      access_token: MAP_TOKEN,
      center: [37.61556, 55.75222],
      map: {},
    };
  },
  emits: ['apiResponded'],

  mounted() {
    this.createMap();
  },

  methods: {
    async createMap() {
      try {
        mapboxgl.accessToken = this.access_token;
        this.map = new mapboxgl.Map({
          container: "map",
          style: "mapbox://styles/mapbox/streets-v11",
          center: this.center,
          zoom: 11,
        });

        let draw = new MapboxDraw({
          displayControlsDefault: false,
          controls: {
            polygon: true,
            trash: true
          },
          defaultMode: 'draw_polygon'
        });

        this.map.addControl(draw, 'bottom-right');
 
        this.map.on('draw.create', API.createArea.bind(this, draw)); 
        this.map.on('load', async () => {
          const response = await API.getPlots()
          response.data.data.forEach(plot => {
            draw.add({
              type: "Feature",
              geometry: { type: 'Polygon', coordinates: plot.attributes.coordinates},
              properties: { strapiId: plot.id }
            })
          });
        });

        this.map.on('contextmenu', (e) => {
          const ids = draw.getSelected().features.map((plot) => {
              return { 
                strapiId: plot.properties.strapiId,
                mapboxId: plot.id
              }
          });

          const mapboxPopup = new mapboxgl.Popup({ closeOnClick: true })
            .setLngLat(e.lngLat)
            .setHTML('<div id="map-popup-content"></div>')
            .addTo(this.map);
          
          nextTick(() => {
            const popupComp = h(MapPopupComponent, {
              strapiId: ids[0].strapiId,
              mapboxId: ids[0].mapboxId,
              drawRef: draw,
              mapboxPopupRef: mapboxPopup,
              onClick: (e) => { }
            });

            render(popupComp, document.getElementById("map-popup-content"));
          });
        });

      } catch (err) {
              console.log("map error", err);
      }
    },
  },
};
</script>

<style scoped>
.main {
  padding: 45px 50px;
}
.flex {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.map-holder {
  width: 100%;
}
#map {
  height: 70vh;
}

</style>