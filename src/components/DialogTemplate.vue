<template>
  <v-dialog
      v-model="dialog"
      max-width="800px"
  >
    <template v-slot:activator="{ on, attrs }">
      <v-btn
          icon
          v-bind="attrs"
          v-on="on"
      >
        <v-icon>mdi-plus</v-icon>
      </v-btn>
    </template>
    <v-card>
      <v-card-title>
        <span class="headline">New Pipeline</span>
      </v-card-title>
      <v-card-text>
        <v-tabs
            vertical
        >
          <v-tab>
            <v-icon left>
              mdi-alpha-c-box
            </v-icon>
          </v-tab>
          <v-tab>
            <v-icon left>
              mdi-alpha-g-box
            </v-icon>
          </v-tab>
          <v-tab>
            <v-icon left>
              mdi-alpha-j-box
            </v-icon>
          </v-tab>
          <v-tab-item>
            <v-card
                class="elevation-1"
            >
              <v-card-text>
                C/C++ Pipeline
                <v-chip
                    class="ma-2"
                >
                  build
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  test
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  deploy
                </v-chip>
              </v-card-text>
            </v-card>
          </v-tab-item>
          <v-tab-item>
            <v-card
                class="elevation-1"
            >
              <v-card-text>
                Go Pipeline
                <v-chip
                    class="ma-2"
                >
                  build
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  test
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  deploy
                </v-chip>
              </v-card-text>
            </v-card>
          </v-tab-item>
          <v-tab-item>
            <v-card
                class="elevation-1"
            >
              <v-card-text>
                Java Pipeline
                <v-chip
                    class="ma-2"
                >
                  build
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  test
                </v-chip>
                <v-icon>mdi-forward</v-icon>
                <v-chip
                    class="ma-2"
                >
                  deploy
                </v-chip>
              </v-card-text>
            </v-card>
          </v-tab-item>
        </v-tabs>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
            text
            @click="closeTemplate"
        >
          Cancel
        </v-btn>
        <v-btn
            text
            @click="createPipeline"
        >
          Create
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'DialogTemplate',
  data() {
    return {
      default: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
      },
      dialog: false,
      pipeline: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
      }
    }
  },
  methods: {
    createPipeline() {
      this.closeTemplate()
      this.$emit('createPipeline', this.pipeline)
    },
    closeTemplate () {
      this.dialog = false
      this.$nextTick(() => {
        this.pipeline = Object.assign({}, this.default)
      })
      this.$emit('closeTemplate')
    }
  },
  watch: {
    dialog (val) {
      val || this.closeTemplate()
    }
  }
}
</script>
