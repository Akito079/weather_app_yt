<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SideNavigation/>
  <RouterView v-slot="{Component}">
    <Transition name="page" mode="out-in">
      <component :is="Component" />
    </Transition>
  </RouterView>
  </div>
</template>

<script>
import { RouterView } from 'vue-router';
import SideNavigation from './components/SideNavigation.vue';
  export default {
    components: { SideNavigation }
}
</script>

<style>
.page-enter-active,
.page-leave-active {
  transition: 600ms ease all;
}

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>