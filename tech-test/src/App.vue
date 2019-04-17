<template>
  <div id="app">
    <Feeds />
    <Map />
  </div>
</template>

<script>
import Map from './components/Map.vue';
import Feeds from './components/Feeds.vue';

export default {
  name: 'app',
  components: {
    Map,
    Feeds
  },
};
</script>

<style lang="scss">
@import "assets/_settings.scss";
  #app {
    @include lg {
      display: grid;
      grid-template-columns: 150px auto;
    }
  }


</style>
