<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="8">
      <v-card>
        <v-card-text>
          <nuxt-content :document="article" />

          <ShareNetwork 
            network="facebook"
            :url="pageUrl"
            :title="article.title" 
            :description="article.description"
          >
            <div class="social green lighten-1 text-center px-5 py-3 white--text">Anderen willen dit ook lezen! Deel het op Facebook</div>
          </ShareNetwork>
          
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
  computed: {
    imageFullPath() {
      return `${process.env.ROOT_PATH}${this.article.afbeelding_voor_listpage}`
    },
    pageUrl() {
      return `${process.env.ROOT_PATH}${this.$route.path}`
    },
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
        { 
          hid: 'og:title', 
          property: 'og:title', 
          content: this.article.title 
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.article.og_description,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: this.imageFullPath,
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
          content: this.article.og_description,
        },
        {
          hid: 'twitter:image',
          name: 'twitter:image',
          content: this.imageFullPath,
        },
        {
          hid: 'twitter:url',
          name: 'twitter:url',
          content: this.pageUrl,
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

.nuxt-content p:nth-child(n+3) {
  text-indent: 20px;
}

.share-network-facebook {
  text-decoration: none;
}

.social {
  font-size: 16px;
}
</style>
