<template lang="pug">
  main
    pm-notification(v-show="showNotification", :success="hasResult")
      p(v-if="hasResult", slot="body") Encontrados: {{ total }}
      p(v-else, slot="body") La búsqueda no ha obtenido resultados
      
    pm-loader(v-show="isLoading")
    section.section(v-show="!isLoading")
      nav.nav
        .container <!-- lo mismo que nav.container --> 
          input.input.is-large(
            type="text", 
            placeholder="Buscar canciones", 
            v-model="searchQuery"
            @keyup.enter="search"
          )
          a.button.is-info.is-large(@click="search") Buscar
          a.button.is-danger.is-large &times;
      .container      
      p
        small {{ searchMessage }}

      .container
        .columns.is-multiline
          .column.is-one-quarter(v-for="track in tracks")
            pm-track(
              v-blur="track.preview_url"
              :class="{ 'is-active': track.id == selectedTrack }",
              :track="track",
              @select="setSelectedTrack"
            )
</template>
<script>
  import trackService from '@/services/track'

  import PmTrack from '@/components/track.vue'

  import PmNotification from '@/components/shared/Notification.vue'
  import PmLoader from '@/components/shared/Loader.vue'

  export default {
    name: 'app',

    components: { PmTrack, PmLoader, PmNotification },

    data () {
      return {
        searchQuery: '',
        tracks: [],

        isLoading: false,

        showNotification: false,

        selectedTrack: '',

        hasResult: false,

        total: 0
      }
    },

    methods: {
      search () {
        if (!this.searchQuery) { return }

        this.isLoading = true
        this.showNotification = false

        trackService.search(this.searchQuery)
          .then(res => {
            console.log(res)
            this.showNotification = true
            this.hasResult = res.tracks.total !== 0
            this.total = res.tracks.total
            this.tracks = res.tracks.items
            this.isLoading = false
          })
      },

      setSelectedTrack (id) {
        this.selectedTrack = id
      }

    },

    computed: {
      searchMessage () {
        return `Encontrados: ${this.tracks.length}`
      }
    },

    watch: {
      showNotification () {
        if (this.showNotification === !this.hasResult) {
          setTimeout(() => {
            this.showNotification = false
          }, 3000)
        }
      }
    }
  }
</script>

<style lang="scss">
  .results {
    margin-top: 50px
  }

  .is-active {
    border: 3px #23d160 solid;
  }

</style>