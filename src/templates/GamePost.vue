<template>
  <Layout>
    <h1>{{ $page.games.title }}</h1>
    <p class="intro">{{ $page.games.excerpt }}</p>
    <VueRemarkContent />
  </Layout>
</template>

<page-query>
query ($id: ID!) {
  games(id: $id) {
    title
    excerpt
  }
}
</page-query>

<script>
export default {
  name: "GamePost",
};
</script>
