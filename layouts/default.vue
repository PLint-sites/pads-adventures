<template>
  <v-app dark>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn v-if="!isHome" class="mr-3" color="primary" nuxt :to="`/`">Home</v-btn>
      <a v-if="desktopSize" href="https://plint-demos.nl" title="Terug naar PLint's Playground">PLint's Playground</a>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
      <v-spacer />
      <a v-if="!desktopSize" href="https://plint-demos.nl" title="Terug naar PLint's Playground">PLint's Playground</a>
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
      desktopSize: false,
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
  },
  mounted() {
    const mediaQuery = window.matchMedia('(max-width: 620px)')
    if (mediaQuery.matches) {
      this.desktopSize = false
    } else {
      this.desktopSize = true
    }
  },
}
</script>
