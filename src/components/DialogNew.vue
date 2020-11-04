<template>
  <v-dialog
      v-model="dialog"
      max-width="500px"
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
        <v-container>
          <v-row>
            <v-col
                cols="12"
                sm="6"
                md="4"
            >
              <v-text-field
                  label="name"
                  required
                  v-model="item.name"
              ></v-text-field>
            </v-col>
            <v-col
                cols="12"
                sm="6"
                md="4"
            >
              <v-text-field
                  label="status"
                  required
                  v-model="item.statue"
              ></v-text-field>
            </v-col>
            <v-col
                cols="12"
                sm="6"
                md="4"
            >
              <v-text-field
                  label="stage"
                  required
                  v-model="item.stage"
              ></v-text-field>
            </v-col>
            <v-col
                cols="12"
                sm="6"
                md="4"
            >
              <v-text-field
                  label="owner"
                  required
                  v-model="item.owner"
              ></v-text-field>
            </v-col>
            <v-col
                cols="12"
                sm="6"
                md="4"
            >
              <v-text-field
                  label="time"
                  required
                  v-model="item.time"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
            text
            @click="saveNew"
        >
          Save
        </v-btn>
        <v-btn
            text
            @click="closeNew"
        >
          Cancel
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'DialogNew',
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
      item: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
      }
    }
  },
  methods: {
    saveNew () {
      this.closeNew()
      this.$emit('saveNew', this.item)
    },
    closeNew () {
      this.dialog = false
      this.$nextTick(() => {
        this.item = Object.assign({}, this.default)
      })
      this.$emit('closeNew')
    }
  },
  watch: {
    dialog (val) {
      val || this.closeNew()
    }
  }
}
</script>
