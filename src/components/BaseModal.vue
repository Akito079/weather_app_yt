<template>
  <Teleport to="body">
    <Transition name="modal-outer">
      <div
        v-show="modalActive"
        class="absolute left-0 top-0 w-full h-screen bg-black bg-opacity-30 flex justify-center px-8"
      >
        <Transition name="modal-inner">
          <div
            v-if="modalActive"
            class="p-4 bg-white self-start mt-32 max-w-screen-md"
          >
            <slot />
            <button
              class="text-white mt-8 bg-weather-primary py-2 px-6"
              @click="$emit('closeModal')"
            >
              Close
            </button>
          </div>
        </Transition>
      </div>
    </Transition>
  </Teleport>
</template>
<script>
export default {
  props: {
    modalActive: {
      type: Boolean,
      default: false,
    },
  },
  emits: ["closeModal"],
};
</script>
<style scoped>
.modal-outer-enter-active,
.modal-outer-leave-active {
  transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}
.modal-outer-enter-from,
.modal-outer-leave-to {
  opacity: 0;
}
.modal-inner-enter-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
}
.modal-inner-leave-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}
.modal-inner-enter-from {
    opacity: 0;
    transform: scale(0.8);
  }

  .modal-inner-leave-to {
    transform: scale(0.8);
  }
</style>
