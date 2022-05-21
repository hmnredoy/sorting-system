<template>
  <div class="text-center">
    <v-dialog
      v-model="dialog"
      width="500"
      persistent
    >
      <v-card>
        <v-card-title class="text-h5 grey lighten-2">
          Success!
        </v-card-title>

        <v-card-text>
            Your Score: <br>
            Number of tries: <strong>{{ tries }}</strong> <br>
            Time Taken: <strong>{{ time }}</strong>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            text
            @click="closeModal"
          >
            Done
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
  export default {
      props: {
          value: {
              type: Boolean,
              default: false
          },
          tries: {
              type: Number,
              default: 0
          },
          time: {
              type: String,
              default: null
          }
      },
      watch: {
          value(val) {
              if (val) {
                this.dialog = true
              }
          }
      },
    data () {
      return {
        dialog: false,
      }
    },
    methods: {
        closeModal() {
            this.dialog = false
            this.$emit('close')
        }
    }
  }
</script>