<template>
  <scroller :list="list"/>
</template>

<script>
import Scroller from "./components/Scroller.vue";

export default {
  components: {
    Scroller
  },
  data() {
    return {
      list: Array.from(new Array(1000).keys())
    }
  }
};
</script>
