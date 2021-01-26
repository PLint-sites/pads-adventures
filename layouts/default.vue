<template>
  <v-app dark>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn v-if="!isHome" color="primary" nuxt :to="`/`">Home</v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Avonturen van Pad',
      isHome: this.$route.path === '/'
    }
  },
  computed: {
    ...mapGetters({
      items: 'toc',
    }),
  },
  watch: {
    $route(to, from) {
      if (to.path === '/') {
        this.isHome = true
      } else {
        this.isHome = false
      }
    }
  }
}
</script>
