<template>
  <nav>
    <router-link to="/">Option API</router-link> |
    <router-link to="/compositionAPI">Composition API</router-link> |
  </nav>
  <router-view/>
</template>

<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: rgb(10, 32, 77);
}
#app {
  padding: 0;
  margin: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgb(10, 32, 77);
}

nav {
  background-color: rgba(255, 255, 255, 0.271);
  border-bottom: 1px solid rgb(10, 32, 77);
  padding: 20px;
  text-align: right;
  height: 20px;
}

nav a {
  font-weight: bold;
  color: rgba(255, 255, 255, 0.434);
}

nav a.router-link-exact-active {
  color: #1f1cd7;
  text-decoration: none;
}
nav a.router-link-active {
  color: #1f1cd7;
  text-decoration: none;
}
  nav a.router-link-active:hover {
  color: #d7b81c;
  text-decoration: none;
}
</style>
