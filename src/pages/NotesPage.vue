<template>
  <v-container fluid class="pa-0">
    <v-container v-if="notesExist">
      <h2>My Notes</h2>
      <p>You currently have no notes. Click the + to create one.</p>
    </v-container>

    <v-layout>
      <v-flex >
        <v-container fluid grid-list-md>
          <v-layout row wrap>
            <v-flex
              xs12
              md6
              lg3
              v-for="note in notes"
              :key="note.id"
            >
              <v-card 
                class="elevation-4" 
                tile 
                ripple
                pa-0 
                :to="{ name: 'note-edit', params: { noteId: note['.key'] }}"
              >
                <v-card-text
                  value=""
                  height="400px"
                  class="pb-2"
                >
                  <div v-bind:class="{ noteContentSmall: isSmallBinding, noteContentLarge: !isSmallBinding }">
                    <h3>
                      {{note.title}}
                    </h3>
                    <p>
                      <span style="white-space: pre-wrap;">{{note.text}}</span>
                    </p>
                  </div>
                </v-card-text>

                <v-divider light></v-divider>
                <v-card-actions class="caption pa-0 pr-3">
                  <v-btn
                    icon
                    @click.prevent="noteInfo"
                  >
                    <v-icon>more_vert</v-icon>
                  </v-btn>

                  <v-spacer></v-spacer>
                  {{ note.modifiedDateFormatted }}
                </v-card-actions>
              </v-card>
            </v-flex>
          </v-layout>
        </v-container>
      </v-flex>
    </v-layout>

    <v-fab-transition>
      <v-btn
        color="secondary"
        dark
        fab
        fixed
        bottom
        right
        :to="{name :'note-add'}"
      >
        <v-icon>add</v-icon>
      </v-btn>
    </v-fab-transition>
  </v-container>
</template>

<script>
import { auth, db } from '../api/firebase'

export default {
  beforeCreate: function () {
    auth.onAuthStateChanged((user) => {
      if (user) {
        this.user = user
        this.$bindAsArray('notes', db.ref(`notes/${user.uid}`).orderByChild('modifiedDate'))
      } else {
        this.$router.replace('login')
      }
    })
  },
  data () {
    return {
      user: {},
      notes: {}
    }
  },
  computed: {
    isSmallBinding () {
      var binding
      if (this.$vuetify.breakpoint.mdAndDown) binding = true
      return binding
    },
    notesExist () {
      return (this.notes.length === 0) && (this.user)
    }
  }
}
</script>

<style>
.noteContentSmall {
    line-height: 1.5em;
    height: 6em;       /* height is {n} times the line-height */
    overflow: hidden;  /* prevents extra lines from being visible */
}
.noteContentLarge {
    line-height: 1.5em;
    height: 15em;       /* height is {n} times the line-height */
    overflow: hidden;  /* prevents extra lines from being visible */
}
</style>
