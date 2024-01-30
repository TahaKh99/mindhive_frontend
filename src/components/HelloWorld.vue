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
    circlesIntersect(outlet1, outlet2) {
    const RADIUS_SUM = 10000; // Sum of radii of two 5KM circles
    const distance = this.distanceBetweenPoints(
      outlet1.latitude, outlet1.longitude,
      outlet2.latitude, outlet2.longitude
    );
    return distance < RADIUS_SUM;
  },

  distanceBetweenPoints(lat1, lon1, lat2, lon2) {
    // Haversine formula to calculate the distance
    const toRad = x => (x * Math.PI) / 180;
    const R = 6371e3; // meters

    const dLat = toRad(lat2 - lat1);
    const dLon = toRad(lon2 - lon1);
    const a = 
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) * 
      Math.sin(dLon / 2) * Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

    return R * c;
  },

  getCircleColor(outlet) {
    // Check if this outlet's circle intersects with any other
    for (let otherOutlet of this.outlets) {
      if (outlet.id !== otherOutlet.id && this.circlesIntersect(outlet, otherOutlet)) {
        return 'red'; // Intersection color
      }
    }
    return 'blue'; // Default color
  },
}
  }

</script>

<style>
.app-container {
  display: flex;
  height: 100vh;
}

.sidebar {
  width: 300px;
  background-color: #2c3e50;
  color: white;
  padding: 25px;
  overflow-y: auto;
}

.sidebar h2 {
  font-size: 2em;
  margin-bottom: 15px;
}

.sidebar input {
  width: 100%;
  padding: 15px;
  margin-bottom: 20px;
  border: none;
  border-radius: 5px;
  font-size: 1em;
}

.outlet-list li {
  background-color: #34495e;
  padding: 15px;
  margin-bottom: 10px;
  border-radius: 5px;
  transition: all 0.3s ease;
  cursor: pointer;
}

.outlet-list li:hover {
  background-color: #4CAF50;
  transform: scale(1.05);
}

.map-container {
  flex-grow: 1;
  position: relative;
}

.popup-content {
  font-size: 1em;
  color: #333;
}
</style>