<template>
  <v-row justify="center" align="center">
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
  </v-row>
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
