<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="8">
      <v-card>
        <v-card-text>
          <nuxt-content :document="article" />
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  async asyncData({ $content, store, params, error }) {
    const slug = params.slug || 'index'
    const article = await $content('avonturen', slug)
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: 'Article not found' })
      })
    store.commit('setTocFromArticle', article.toc)
    const [prev, next] = await $content('avonturen')
      .only(['title', 'slug'])
      .sortBy('title')
      .surround(params.slug)
      .fetch()

    return {
      article,
      prev,
      next,
    }
  },
  head() {
    return {
      title: this.article.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.article.description,
        },
        // Open Graph
        { hid: 'og:title', property: 'og:title', content: this.article.title },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.article.description,
        },
        // Twitter Card
        {
          hid: 'twitter:title',
          name: 'twitter:title',
          content: this.article.title,
        },
        {
          hid: 'twitter:description',
          name: 'twitter:description',
          content: this.article.description,
        },
      ],
    }
  },
}
</script>
<style>
.nuxt-content h1 {
  line-height: 30px;
  margin-bottom: 20px;
}

.nuxt-content img {
  width: 100%;
  margin-left: -20px;
}

.nuxt-content p {
  text-indent: 20px;
}
</style>
