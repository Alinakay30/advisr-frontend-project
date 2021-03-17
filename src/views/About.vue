<template>
  <div class="about">

    <router-link :to="{ name: 'Home' }">Home</router-link>
    <h1>{{ name }}</h1>
    <div v-for="business in list" :key="business.name">
      <div hidden id="display" v-if="business.name === name">
        {{ latLocation = business.location.lat }}
        {{ lngLocation = business.location.lng }}
      </div>
    </div>
  </div>

  <div id="map"></div>
  <div id="popup" class="ol-popup">
    <div id="popup-content"></div>
  </div>
</template>

<script>
import jsonData from "@/business-list.json";
import "ol/ol.css";

export default {
  name: "About",
  el: "#about",
  data() {
    return {
      list: jsonData,
      name: this.$route.params.name,
      latLocation: "",
      lngLocation: "",
    };
  },

  mounted: function () {

    var map = new ol.Map({
      controls: ol.control.defaults({ attribution: false }),
      layers: [
        new ol.layer.Tile({
          source: new ol.source.OSM({
            url: "https://tile.openstreetmap.org/{z}/{x}/{y}.png",
            attributions: [
              ol.source.OSM.ATTRIBUTION,
            ],
          }),
        }),
      ],
      target: "map",

      view: new ol.View({
        center: ol.proj.fromLonLat([this.lngLocation, this.latLocation]),
        maxZoom: 19,
        minZoom: 12,
        zoom: 15,
      }),
    });

    var style = new ol.style.Style({
      image: new ol.style.Circle({
        radius: 6,
        fill: new ol.style.Fill({
          color: "red",
        }),
      }),
    });

    var layer = new ol.layer.Vector({
      source: new ol.source.Vector({
        features: [
          new ol.Feature({
            geometry: new ol.geom.Point(
              ol.proj.fromLonLat([this.lngLocation, this.latLocation])
            ),
          }),
        ],
      }),
      style: style,
    });

    map.addLayer(layer);

    var container = document.getElementById("popup");
    var content = document.getElementById("popup-content");

    var overlay = new ol.Overlay({
      element: container,
      autoPan: true
    });
    map.addOverlay(overlay);

    map.on("singleclick", function (event) {
      if (map.hasFeatureAtPixel(event.pixel) === true) {
        var coordinate = event.coordinate;

        content.innerHTML = "<b>I am located here.</b><br/>";
        overlay.setPosition(coordinate);
      } else {
        overlay.setPosition(undefined);
      }
    });
  },
};
</script>



<style scoped>
.displayName {
  text-align: center;
}

#map {
  position: relative;
  overflow: hidden;
  margin-left: auto;
  margin-right: auto;
  padding-top: 70px;
  width: 70% !important;
  height: 60% !important;
  touch-action: none;
}

#popup-content {
  font-weight: bolder;
}

h1 {
  font-weight: bold;
  margin-top: 30px;
}

</style>