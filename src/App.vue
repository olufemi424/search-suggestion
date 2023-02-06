<template>
  <Search />
</template>


<script>
import { defineComponent } from 'vue';
import Search from './components/Search.vue'

export default defineComponent({
  name: 'App',
  components: {
    Search
  },
  setup() {
    return { };
  },
});
</script>
