<template>
  <div class="language-switcher">
    <nuxt-link
      v-for="locale in availableLocales"
      :key="locale.code"
      :to="switchLocalePath(locale.code)"
      >{{ locale.name }}</nuxt-link
    >
  </div>
</template>
<script>
export default {
  computed: {
    availableLocales() {
      return this.$i18n.locales.filter(
        (locale) => locale.code !== this.$i18n.locale
      );
    },
  },
  methods: {
    switchLocalePath(code) {
      const currentRoute = this.$nuxt.$route;
      if (currentRoute.path === "/index.html") {
        return `/${code}/`;
      }
      const firstSegment = currentRoute.path.split("/")[1];
      if (
        this.$i18n.locales.map((locale) => locale.code).includes(firstSegment)
      ) {
        return currentRoute.path.replace(firstSegment, code);
      }
      return `/${code}${currentRoute.path}`;
    },
  },
};
</script>
<style>
.language-switcher {
  position: absolute;
  top: 20px;
  right: 20px;
}
</style>
