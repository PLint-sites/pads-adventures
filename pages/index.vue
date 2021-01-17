<template>
  <div id="grid">
    <div class="grid-item" v-for="(article, index) of articles" :key="index" :style="background(article.afbeelding_voor_listpage)">
      <NuxtLink class="link-to-chapter" :to="`/${article.slug}`">
        <div class="green lighten-1 text-center">
          <h3 class="white--text">{{article.title}}</h3>
        </div>
      </NuxtLink>
    </div>
    <template v-if="articles.length < 4">
      <div class="grid-item coming-soon" v-for="index in (4-articles.length)" :key="`${index}_1`">Coming soon</div>
    </template>
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
}

#grid .grid-item .link-to-chapter {
  display: block;
  width: 100%;
  height: 100%;
  padding: 10px;
}

.grid-item.coming-soon {
  border: 2px dashed #1B5E20;
  padding: 10px;
}
</style>
