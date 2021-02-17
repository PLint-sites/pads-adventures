<template>
  <div id="grid">
    <div class="grid-item" v-for="(article, index) of articles" :key="index" :style="background(article.afbeelding_voor_listpage)">
      <NuxtLink class="link-to-chapter" :to="`/${article.slug}`">
        <div class="green lighten-1 text-center">
          <h3 class="white--text">{{article.title}}</h3>          
        </div>
        <p v-if="articleIsNew(article)" class="red lighten-1 white--text">Nieuw</p>
      </NuxtLink>
    </div>
  </div>
</template>

<script>
import dayjs from 'dayjs'
export default {
  async asyncData({ $content, store, params, error }) {
    const slug = params.slug || 'index'
    let articles = await $content('avonturen')
      .sortBy('publishing_date', 'desc')
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: 'Article not found' })
      })
    // make sure that locally we can see unpublished adventures
    if (!process.env.local) {
      articles = articles.filter(item => item.published)
    }
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
    articleIsNew(article) {
      const difference = (dayjs().unix() - dayjs(article.publishing_date).unix())/86400
      if (difference < 1) {
        return true
      }
      return false
    }
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
  position: relative;
}

#grid .grid-item .link-to-chapter {
  display: block;
  width: 100%;
  height: 100%;
  padding: 10px;
}

.grid-item p.red {
  position: absolute;
  bottom: -10px;
  right: 10px;
  display: inline;
  padding: 3px 8px;
}
</style>
