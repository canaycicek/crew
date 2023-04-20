<template>
  <div
    class="d-flex align-items-center mt-4 p-3 rounded justify-content-between transition music"
    :class="{ ['dark active']: active, gray: !active }"
    @click="$emit('click')"
  >
    <div class="d-flex align-items-center">
      <img
        width="30"
        :src="$router.options.base + 'images/' + music.image"
        class="me-3"
      />
      <div>{{ music.name }}</div>
    </div>
    <!-- <font-awesome-icon :icon="['fas', 'pause']" v-if="active" />
    <font-awesome-icon :icon="['fas', 'play']" v-else /> -->
  </div>
</template>
<script>
export default {
  props: {
    music: {
      type: Object,
      required: true,
    },
    active: {
      type: Boolean,
      required: true,
    },
  },
};
</script>
<style>
.music {
  cursor: pointer;
}

.music:not(.active):hover {
  background-color: #1a1a1a;
}
</style>
