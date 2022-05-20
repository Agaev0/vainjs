<template>
<div>
  <div class="home" >
    <div class="home-button"><a> <RouterLink :to="{name: 'home'}">Home</RouterLink></a> </div>
    <div class="home-button"><a>  <RouterLink :to="{name: 'products'}">About</RouterLink></a></div>
    </div>
    <RouterView></RouterView>
    
  </div>
</template>

<script lang="ts">
export default {
  name: "App",
};
</script>

<style scoped>
.home{ display:flex;}

.home-button{border: solid rgba(87, 87, 234, 0.247);
 margin: 0px 5px;}
 a{text-decoration: none; font-size: 18px;}
 .home-button:hover{background-color: brown; transition: .5s;}
 a:hover{color:white; transition: .5s;}
 
</style>
