<template>
  <div class="app-container">
    <aside class="sidebar">
      <h2>Outlets in Melaka</h2>
      <div class="toggle-container">
  <label class="switch">
    <input type="checkbox" id="toggleRadius" v-model="showRadius">
    <span class="slider round"></span>
  </label>
  <label for="toggleRadius" class="toggle-label">Show Radius</label>
</div>

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
        <l-marker 
            v-for="outlet in outlets" 
            :key="outlet.id" 
            :lat-lng="[outlet.latitude, outlet.longitude]" 
            :icon="customIcon">
        <l-popup><div class="popup-content">{{ outlet.name }}</div></l-popup>
      </l-marker>
      <template v-if="showRadius">
      <l-circle 
          v-for="outlet in outlets" :key="outlet.id" 
          :lat-lng="[outlet.latitude, outlet.longitude]" 
          :radius="5000"
          :color="getCircleColor(outlet)">
      </l-circle>
    </template>
      </l-map>
    </div>
  </div>
</template>


<script>
import axios from 'axios';
import { LMap, LTileLayer, LMarker, LPopup, LCircle } from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';
import mapMarker from '@/assets/map_marker.png';
import L from 'leaflet';

export default {
  name: 'MindHiveApp',
  components: {
    LMap, LTileLayer, LMarker, LPopup, LCircle
  },
  data() {
    return {
      outlets: [],
      center: [2.2560, 102.3105],
      zoom: 12.2,
      mapStyle: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: 'Map data © OpenStreetMap contributors',
      searchQuery: '',
      customIcon: L.icon({
      iconUrl: mapMarker,
      iconSize: [40, 40], 
      iconAnchor: [20, 40], 
      popupAnchor: [0, -40], 
    }),
    showRadius: true, 

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
    console.log(`Api-Key ${process.env.VUE_APP_API_KEY}`)

  },
  methods: {
    fetchOutlets(url) {
      axios.get(url, {
        headers: {
      'Authorization': `Api-Key ${process.env.VUE_APP_API_KEY}`
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
  width: 90%;
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
.toggle-container {
  display: flex;
  align-items: center;
  margin-top: 20px;
  margin-bottom: 20px;
}

.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
  margin-right: 10px;
}

.switch input { 
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: .4s;
  transition: .4s;
}

input:checked + .slider {
  background-color: #4CAF50;
}

input:focus + .slider {
  box-shadow: 0 0 1px #4CAF50;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

.toggle-label {
  font-size: 1em;
  user-select: none;
}
</style>