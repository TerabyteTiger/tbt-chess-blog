<template>
  <div class="layout">
    <header class="header">
      <div class="header-flex">
        <img :src="image[theme]" alt="" height="50" @click="changeTheme()" />
        <strong>
          <g-link to="/">TBT Chess</g-link>
        </strong>
      </div>
      <nav class="nav">
        <g-link class="nav__link" to="/">Home</g-link>
        <g-link class="nav__link" to="/about/">About</g-link>
      </nav>
    </header>
    <slot />
  </div>
</template>

<script>
export default {
  data() {
    return {
      theme: 1,
      options: ["standard", "purple", "pink", "red", "blue", "green", "grey"],
      image: [
        "/standard.svg",
        "/purple.svg",
        "/pink.svg",
        "/red.svg",
        "/blue.svg",
        "/green.svg",
        "/grey.svg",
      ],
    };
  },
  methods: {
    changeTheme() {
      this.theme = this.theme + 1 >= this.options.length ? 0 : this.theme + 1;
    },
  },
};
</script>

<static-query>
query {
  metadata {
    siteName
  }
}
</static-query>

<style>
body {
  font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, sans-serif;
  margin: 0;
  padding: 0;
  line-height: 1.5;
}

.layout {
  max-width: 760px;
  margin: 0 auto;
  padding-left: 20px;
  padding-right: 20px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
  height: 80px;
}

.header-flex {
  display: flex;
  align-items: center;
}

.nav__link {
  margin-left: 20px;
}
</style>
