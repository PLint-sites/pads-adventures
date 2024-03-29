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
  data() {
    return {
      title: 'Avonturen van Pad',
      description: 'Pad en pad gaan op avontuur: ze moeten pad blauw redden',
      image: '/images/proloog-list-avonturen-van-pad.jpg',
    }
  },
  computed: {
    imageFullPath() {
      return `${process.env.ROOT_PATH}${this.image}`
    },
  },
  head() {
    return {
      meta: [
        // Open Graph
        { 
          hid: 'og:title', 
          property: 'og:title', 
          content: this.title
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.description,
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
          content: this.title,
        },
        {
          hid: 'twitter:description',
          name: 'twitter:description',
          content: this.description,
        },
        {
          hid: 'twitter:image',
          name: 'twitter:image',
          content: this.imageFullPath,
        },
      ],
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
