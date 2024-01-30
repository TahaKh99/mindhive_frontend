<template>
  <div class="app-container">
    <aside class="sidebar">
      <h2>Outlets in Melaka</h2>
      <input type="text" v-model="searchQuery" placeholder="Search outlets..." />
      <ul class="outlet-list">
        <li v-for="outlet in filteredOutlets" :key="outlet.id" @click="centerMapOnOutlet(outlet)">
          <h3>{{ outlet.name }}</h3>
          <p>{{ outlet.address }}</p>
        </li>
      </ul>
    </aside>
    <div class="map-container">
      <l-map :zoom="zoom" :center="center" style="height: 100%;">
        <l-tile-layer :url="mapStyle" :attribution="attribution"></l-tile-layer>
        <l-marker v-for="outlet in outlets" :key="outlet.id" :lat-lng="[outlet.latitude, outlet.longitude]">
          <l-popup><div class="popup-content">{{ outlet.name }}</div></l-popup>
        </l-marker>
        <l-circle v-for="outlet in outlets" :key="outlet.id" 
                  :lat-lng="[outlet.latitude, outlet.longitude]" 
                  :radius="5000"
                  :color="getCircleColor(outlet)">
        </l-circle>
      </l-map>
    </div>
  </div>
</template>


<script>
import axios from 'axios';
import { LMap, LTileLayer, LMarker, LPopup, LCircle } from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';

export default {
  name: 'HelloWorld',
  components: {
    LMap, LTileLayer, LMarker, LPopup, LCircle
  },
  data() {
    return {
      outlets: [],
      center: [2.1960, 102.2405],
      zoom: 13,
      mapStyle: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: 'Map data © OpenStreetMap contributors',
      searchQuery: ''
    };
  },
  computed: {
    filteredOutlets() {
      return this.outlets.filter(outlet =>
        outlet.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    }
  },
  created() {
    this.fetchOutlets('https://tahakh.pythonanywhere.com/api/outlets/');
  },
  methods: {
    fetchOutlets(url) {
      axios.get(url, {
        headers: {
          'Authorization': `Api-Key avN8F1Ez.lQgxfHtm3OP26XQBIMp6HSAPCI8roFaT`
        }
      })
      .then(response => {
        this.outlets = this.outlets.concat(response.data.results);
        if (response.data.next) {
          this.fetchOutlets(response.data.next);
        }
      })
      .catch(error => {
        console.error("There was an error fetching the outlets:", error);
      });
    },
    centerMapOnOutlet(outlet) {
      this.center = [outlet.latitude, outlet.longitude];
      this.zoom = 15;
    },
    getCircleColor() {
      // Add logic for color based on intersection
      return 'blue'; // Default color
    }
  }
}
</script>


<style>
.app-container {
  display: flex;
  height: 100vh;
}

.sidebar {
  width: 25%;
  background-color: #ecf0f1;
  box-shadow: 2px 0 5px rgba(0,0,0,0.1);
  padding: 20px;
  overflow-y: auto;
}

.sidebar h2 {
  color: #34495e;
  font-size: 1.8em;
}

.sidebar input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  border: none;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.outlet-list li {
  background-color: white;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
}

.outlet-list li:hover {
  background-color: #bdc3c7;
  transform: scale(1.02);
}

.map-container {
  flex-grow: 1;
  border-left: 1px solid #bdc3c7;
}

.popup-content {
  font-size: 1em;
  color: #2c3e50;
}
</style>