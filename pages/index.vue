<template>
  <!--<v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card v-for="(article, index) of articles" :key="index" style="margin-bottom: 30px;">
        <v-card-title class="headline">{{article.title}}</v-card-title>
        <v-card-text>
          {{article.description}}
          <img v-if="article.afbeelding_voor_listpage" :src="`${article.afbeelding_voor_listpage}`" alt="">
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" nuxt :to="`/${article.slug}`">{{article.title}}</v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>-->
  <div id="grid">
    <div class="grid-item" v-for="(article, index) of articles" :key="index">
      <img v-if="article.afbeelding_voor_listpage" :src="`${article.afbeelding_voor_listpage}`">
      <br>
      <v-btn color="primary" nuxt :to="`/${article.slug}`">{{article.title}}</v-btn>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ $content, store, params, error }) {
    const slug = params.slug || 'index'
    const articles = await $content('avonturen')
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: 'Article not found' })
      })
    store.commit('setTocFromArticles', articles)
    console.log(articles)
    return {
      articles,
    }
  },
}
</script>

<style lang="css" scoped>
#grid {
  display: grid;
  grid-gap: 30px;
  grid-template-columns: repeat( auto-fit, minmax(350px, 1fr) );
}

.grid-item {
  position: relative;
  border: 5px solid grey;
}

#grid .grid-item img {
  width: 100%;
}

#grid .grid-item a {
  position: absolute;
  bottom: 0;
  right: 0;
}
</style>
