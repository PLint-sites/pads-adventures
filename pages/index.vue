<template>
  <div id="grid">
    <!--<div class="grid-item" v-for="(article, index) of articles" :key="index">
      <img v-if="article.afbeelding_voor_listpage" :src="`${article.afbeelding_voor_listpage}`">
      <v-btn color="primary" nuxt :to="`/${article.slug}`">{{article.title}}</v-btn>
    </div>-->
    <div class="grid-item" v-for="(article, index) of articles" :key="index" :style="background(article.afbeelding_voor_listpage)">
      <NuxtLink class="link-to-chapter" :to="`/${article.slug}`">
        <div class="green lighten-1 text-center">
          <h3 class="white--text">{{article.title}}</h3>
        </div>
      </NuxtLink>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ $content, store, params, error }) {
    const slug = params.slug || 'index'
    let articles = await $content('avonturen')
      .sortBy('publishing_date')
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: 'Article not found' })
      })
    
    articles = articles.filter(item => item.published)
    store.commit('setTocFromArticles', articles)
    return {
      articles,
    }
  },
  methods: {
    background(filename) {
      return {
        backgroundImage: `url(${filename})`,
        backgroundSize: 'cover'
      }
    },
  },
}
</script>

<style lang="css" scoped>
#grid {
  display: grid;
  grid-gap: 30px;
  grid-template-columns: repeat( auto-fit, minmax(280px, 1fr) );
}

.grid-item {
  width: 100%;
  height: 250px;
  border: 5px solid #1B5E20;
  padding: 10px;
}

#grid .grid-item .link-to-chapter {
  display: block;
  width: 100%;
}
</style>
