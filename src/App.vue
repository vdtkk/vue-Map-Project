<template>
  <div>
    <div ref="map" style="height: 600px;"></div>
  </div>
</template>

<script>
import L from 'leaflet'; // veya 'mapbox-gl' for Mapbox
import 'leaflet/dist/leaflet.css';

export default {
  data() {
    return {
      map: null,
      marker: null,
      blinkingInterval: null,
      isBlinking: true,
  
    };
  },
  mounted() {
    this.initializeMap();
    this.loadGeoJSON();
    this.addBlinkingMarker();
  },
  methods: {
    initializeMap() {
    
      this.map = L.map(this.$refs.map).setView([39.92632, 32.83592], 14.99);  
      
     
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: ' ',
      }).addTo(this.map);  
    },

    addBlinkingMarker() {
      const svgIcon = '<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zM11 7h2v6h-2zm0 8h2v2h-2z"/></svg>';
      const markerIcon = L.divIcon({
        className: 'blinking-marker',
        
        html: svgIcon,  
      });
    },


    loadGeoJSON() {
     
      const geojsonUrl = 'map.geojson';
      
      fetch(geojsonUrl)
        .then(response => response.json())
        .then(geojsonData => {
          // GeoJSON verilerini haritaya ekler
          L.geoJSON(geojsonData).addTo(this.map);  
        })
        .catch(error => {
          console.error('Error loading GeoJSON:', error);
        });this.marker = L.marker([39.9334, 32.8597], { icon: markerIcon }).addTo(this.map);

// İkonun yanıp sönme efekti için bir zamanlayıcı başlat
this.blinkingInterval = setInterval(() => {
  this.isBlinking = !this.isBlinking;
  const iconHtml = this.isBlinking ? '<i class="fas fa-star"></i>' : '<i class="far fa-star"></i>';
  this.marker.setIcon(L.divIcon({ className: 'blinking-marker', html: iconHtml }));
}, 500);
    }, 
    beforeDestroy() {
    
    clearInterval(this.blinkingInterval);
  },
  },
};
</script>

<style>
#map {
  height: 100%;
}
.blinking-marker {
  display: inline-block;
  font-size: 24px;
  color: yellow;
  animation: blink 1s infinite;
}

@keyframes blink {
  0%, 50% {
    opacity: 1;
  }
  51%, 100% {
    opacity: 0;
  }
}
</style>