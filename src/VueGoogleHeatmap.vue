<template>
  <div ref="map" :style="`width: ${mapWidth}; height: ${mapHeight}`"></div>
</template>

<script>
import { loaded } from './loader';

export default {
  name: 'vue-google-heatmap',
  props: {
    lat: {
      type: Number,
      default: () => 37.775,
    },
    lng: {
      type: Number,
      default: () => -122.434,
    },
    initialZoom: {
      type: Number,
      default: () => 13,
    },
    opacity: {
      type: Number,
      default: () => 0.6,
    },
    gradient: {
      type: Array,
    },
    radius: {
      type: Number,
    },
    mapType: {
      type: String,
      default: () => 'roadmap',
    },
    points: {
      type: Array,
      required: true,
    },
    width: {
      type: [String, Number],
      default: () => '100%',
    },
    height: {
      type: [String, Number],
      default: () => '100%',
    },
  },
  watch: {
    points: function(old) {
      if (this.$heatmap) {
        this.$heatmap.setData(this.heatmapPoints);
      } else {
        const heatmapConfig = {
          data: this.heatmapPoints,
          map: this.$mapObject,
          opacity: this.opacity,
        };
        if (this.gradient) heatmapConfig.gradient = this.gradient;
        if (this.radius) heatmapConfig.radius = this.radius;
        this.$heatmap = new google.maps.visualization.HeatmapLayer(
          heatmapConfig,
        );
        this.$heatmap.setMap(this.$mapObject);
      }
    },
  },
  data() {
    return {
      mapStyle: [
        {
          featureType: 'administrative',
          elementType: 'all',
          stylers: [
            {
              saturation: '-100',
            },
          ],
        },
        {
          featureType: 'administrative.province',
          elementType: 'all',
          stylers: [
            {
              visibility: 'off',
            },
          ],
        },
        {
          featureType: 'landscape',
          elementType: 'all',
          stylers: [
            {
              saturation: -100,
            },
            {
              lightness: 65,
            },
            {
              visibility: 'on',
            },
          ],
        },
        {
          featureType: 'poi',
          elementType: 'all',
          stylers: [
            {
              saturation: -100,
            },
            {
              lightness: '50',
            },
            {
              visibility: 'simplified',
            },
          ],
        },
        {
          featureType: 'road',
          elementType: 'all',
          stylers: [
            {
              saturation: '-100',
            },
          ],
        },
        {
          featureType: 'road.highway',
          elementType: 'all',
          stylers: [
            {
              visibility: 'simplified',
            },
          ],
        },
        {
          featureType: 'road.arterial',
          elementType: 'all',
          stylers: [
            {
              lightness: '30',
            },
          ],
        },
        {
          featureType: 'road.local',
          elementType: 'all',
          stylers: [
            {
              lightness: '40',
            },
          ],
        },
        {
          featureType: 'transit',
          elementType: 'all',
          stylers: [
            {
              saturation: -100,
            },
            {
              visibility: 'simplified',
            },
          ],
        },
        {
          featureType: 'water',
          elementType: 'geometry',
          stylers: [
            {
              hue: '#ffff00',
            },
            {
              lightness: -25,
            },
            {
              saturation: -97,
            },
          ],
        },
        {
          featureType: 'water',
          elementType: 'labels',
          stylers: [
            {
              lightness: -25,
            },
            {
              saturation: -100,
            },
          ],
        },
      ],
    };
  },
  computed: {
    mapWidth() {
      if (typeof this.width === 'string') {
        return this.width;
      } else {
        return `${this.width}px`;
      }
    },
    mapHeight() {
      if (typeof this.height === 'string') {
        return this.height;
      } else {
        return `${this.height}px`;
      }
    },
    heatmapPoints() {
      return this.points.map(
        (point) => new google.maps.LatLng(point.lat, point.lng),
      );
    },
  },
  created() {
    return loaded.then(() => {
      const mapElement = this.$refs.map;
      this.$mapObject = new google.maps.Map(mapElement, {
        zoom: this.initialZoom,
        center: { lat: this.lat, lng: this.lng },
        mapTypeId: this.mapType,
        streetViewControl: false,
        styles: this.mapStyle,
      });
    });
  },
};
</script>
