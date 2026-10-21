<template>
  <the-nav/>
  <router-view/>
  <the-footer/>
</template>
<script>
import TheFooter from "@/components/TheFooter.vue";
import TheNav from "@/components/TheNav.vue";

export default {
  components: {TheNav, TheFooter},
  setup() {
    const url = '';

    return {
      url,
    }
  }
}
</script>
<style>
</style>
