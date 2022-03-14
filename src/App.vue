<template>
  <div class="main">
    <!-- <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div> -->
    <!-- <router-view /> -->
    <Bubble></Bubble>
  </div>
</template>

<script>
import Bubble from "@/views/Bubble.vue";
export default {
  components: {
    Bubble,
  },
};
</script>
<style lang="scss">
.main {
  height: 100vh;
}
</style>
