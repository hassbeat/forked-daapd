<template>
  <div>
    <tabs-music></tabs-music>

    <content-with-heading>
      <template slot="options">
        <index-button-list :index="index_list"></index-button-list>
      </template>
      <template slot="heading-left">
        <p class="title is-4">Genres</p>
        <p class="heading">{{ genres.total }} genres</p>
      </template>
      <template slot="content">
        <list-item-genre v-for="genre in genres.items" :key="genre.name" :genre="genre" @click="open_genre(genre)">
          <template slot="actions">
            <a @click="open_dialog(genre)">
              <span class="icon has-text-dark"><i class="mdi mdi-dots-vertical mdi-18px"></i></span>
            </a>
          </template>
        </list-item-genre>
        <modal-dialog-genre :show="show_details_modal" :genre="selected_genre" @close="show_details_modal = false" />
      </template>
    </content-with-heading>
  </div>
</template>

<script>
import { LoadDataBeforeEnterMixin } from './mixin'
import ContentWithHeading from '@/templates/ContentWithHeading'
import TabsMusic from '@/components/TabsMusic'
import IndexButtonList from '@/components/IndexButtonList'
import ListItemGenre from '@/components/ListItemGenre'
import ModalDialogGenre from '@/components/ModalDialogGenre'
import webapi from '@/webapi'

const genresData = {
  load: function (to) {
    return webapi.library_genres()
  },

  set: function (vm, response) {
    vm.genres = response.data
  }
}

export default {
  name: 'PageGenres',
  mixins: [ LoadDataBeforeEnterMixin(genresData) ],
  components: { ContentWithHeading, TabsMusic, IndexButtonList, ListItemGenre, ModalDialogGenre },

  data () {
    return {
      genres: { items: [] },

      show_details_modal: false,
      selected_genre: {}
    }
  },

  computed: {
    index_list () {
      return [...new Set(this.genres.items
        .map(genre => genre.name.charAt(0).toUpperCase()))]
    }
  },

  methods: {
    open_genre: function (genre) {
      this.$router.push({ name: 'Genre', params: { genre: genre.name } })
    },

    open_dialog: function (genre) {
      this.selected_genre = genre
      this.show_details_modal = true
    }
  }
}
</script>

<style>
</style>
