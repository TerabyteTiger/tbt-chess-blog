<template>
  <Layout>
    <div v-for="game in $page.games.edges" :key="game.node.id">
      <g-link :to="game.node.path">
        <h2>{{ game.node.title }}</h2>
      </g-link>
      <p>{{ game.node.excerpt }}</p>
    </div>
  </Layout>
</template>

<page-query>
query  {
  games: allGames(sortBy: "date", order: DESC){
    edges {
      node{
        id
        title
        excerpt
        path
        date
      }
    }
  }
}
</page-query>
