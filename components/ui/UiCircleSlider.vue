<template>
  <client-only placeholder="loading...">
    <div>
      <vue-slider
        v-on="$listeners"
        v-bind="$attrs"
        :value="value"
        @change="$emit('input', $event)"
        start-angle="90"
        range-color=""
        line-cap="round"
        :width="5"
        :radius="105"
        :showTooltip="false"
        :mouse-scroll-action="true"
      />
    </div>
  </client-only>
</template>

<script>
export default {
  components: {
    VueSlider: () => import("vue-round-slider"),
  },
  props: {
    value: {
      type: Number,
      default: 0,
    },
    label: {
      type: String,
      default: null,
    },
  },
};
</script>
<style>
.rs-path {
  stroke: transparent !important;
}

.rs-svg-mode .rs-range {
  stroke: var(--primary-color) !important;
}
</style>
