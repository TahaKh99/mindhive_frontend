<template>
  <div id="app">
    <nav class="navbar">
      <div class="logo">Outlet Locator</div>
      <div class="nav-links">
        <a href="#home">Home</a>
        <a href="#contact">Contact</a>
        <a href="#about">About</a>
      </div>
    </nav>
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  color: #333;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #34495e;
  color: white;
  padding: 10px 20px;
}

.navbar .logo {
  font-weight: bold;
  font-size: 1.5em;
}

.navbar .nav-links a {
  color: white;
  text-decoration: none;
  margin-left: 20px;
}

.navbar .nav-links a:hover {
  text-decoration: underline;
}
</style>
