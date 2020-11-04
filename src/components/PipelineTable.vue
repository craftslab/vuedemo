<template>
  <v-card>
    <v-data-table
        :headers="headers"
        :items="pipelines"
        :sort-by.sync="sortBy"
        :sort-desc.sync="sortDesc"
        class="elevation-1"
        show-select
        v-model="selected"
    >
      <template v-slot:top>
        <v-toolbar
            dense
            flat
            text
        >
          <v-toolbar-title>Pipeline</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-col
              md="2"
          >
            <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details="auto"
            ></v-text-field>
          </v-col>
          <dialog-new @closeNew="closeNew" @saveNew="saveNew"></dialog-new>
          <v-dialog v-model="dialogView" max-width="500px">
            <v-card>
              <v-card-title class="headline">View Pipeline</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeView">Close</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="headline">Delete Pipeline</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="confirmDelete">OK</v-btn>
                <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.status="{ item }">
        <v-card-text
            class="ma-2"
        >
          <a href='#'>#1</a>
          <v-icon
              right
              small
          >
            {{ getStatus(item) }}
          </v-icon>
        </v-card-text>
      </template>
      <template v-slot:item.stage ="{ }">
        <v-layout
            justify-center
        >
          <v-flex sm2>
            <div>build</div>
            <div id="build">
              <v-icon
                  color="grey"
                  x-small
              >
                mdi-record
              </v-icon>
            </div>
          </v-flex>
          <v-flex sm2>
            <div>test</div>
            <div id="test">
              <v-icon
                  color="grey"
                  x-small
              >
                mdi-record
              </v-icon>
            </div>
          </v-flex>
          <v-flex sm2>
            <div>deploy</div>
            <div id="deploy">
              <v-icon
                  color="grey"
                  x-small
              >
                mdi-record
              </v-icon>
            </div>
          </v-flex>
        </v-layout>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-menu>
          <template v-slot:activator="{ on: menu }">
            <v-tooltip bottom>
              <template v-slot:activator="{}">
                <v-btn icon v-on="{ ...menu }">
                  <v-icon>mdi-dots-vertical</v-icon></v-btn
                >
              </template>
            </v-tooltip>
          </template>
          <v-list class="pa-0" dense>
            <v-list-item
                v-for="action in actions"
                :key="action.text"
                @click="action.click(item)"
            >
              <v-list-item-icon class="mr-2">
                <v-icon small>{{ action.icon }}</v-icon>
              </v-list-item-icon>
              <v-list-item-title>{{ action.text }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
import DialogNew from './DialogNew.vue'
//import DialogView from './DialogView.vue'
export default {
  name: 'PipelineTable',
  components: {
    DialogNew,
    //DialogView
  },
  data() {
    return {
      actions: [
        { text: 'View', icon: 'mdi-eye', click: this.viewItem },
        { text: 'Delete', icon: 'mdi-close', click: this.deleteItem }
      ],
      dialogDelete: false,
      dialogView: false,
      headers: [
        { text: 'Name', align: 'left', sortable: true, value: 'name' },
        { text: 'Status', align: 'center', sortable: false, value: 'status' },
        { text: 'Stage', align: 'center', sortable: false, value: 'stage' },
        { text: 'Owner', align: 'left', sortable: true, value: 'owner' },
        { text: 'Time', align: 'left', sortable: true, value: 'time' },
        { text: 'Actions', align: 'center', value: 'actions', sortable: false }
      ],
      index: -1,
      pipelines: [],
      search: '',
      selected: [],
      sortBy: 'time',
      sortDesc: true,
      stage: {
        build: 2,
        test: 3,
        deploy: 4
      },
      status: {
        fail: 'mdi-close-circle-outline',
        pass: 'mdi-check-circle-outline',
        running: 'mdi-rotate-left'
      }
    }
  },
  created () {
    this.initialize()
  },
  methods: {
    initialize () {
      this.pipelines = [
        {
          name: 'C/C++ 2020-10-20',
          status: 'fail',
          stage: 'build',
          owner: 'allen@example.com',
          time: '2020-10-20 00:00:00'
        },
        {
          name: 'Go 2020-10-20',
          status: 'pass',
          stage: 'test',
          owner: 'bob@example.com',
          time: '2020-10-20 00:00:01'
        },
        {
          name: 'Java 2020-10-20',
          status: 'running',
          stage: 'deploy',
          owner: 'cathy@example.com',
          time: '2020-10-20 00:00:02'
        }
      ]
    },
    deleteItem (item) {
      this.index = this.pipelines.indexOf(item)
      this.dialogDelete = true
    },
    viewItem (item) {
      Object.assign({}, item)
      this.dialogView = true
    },
    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.index = -1
      })
    },
    closeNew () {
      this.$nextTick(() => {
        this.index = -1
      })
    },
    closeView () {
      this.dialogView = false
    },
    confirmDelete () {
      this.pipelines.splice(this.index, 1)
      this.closeDelete()
    },
    saveNew(item) {
      this.pipelines.push(item)
      this.closeNew()
    },
    getStage (item) {
      return this.stage[item.stage]
    },
    getStatus (item) {
      return this.status[item.status]
    }
  },
  watch: {
    dialogDelete (val) {
      val || this.closeDelete()
    }
  }
}
</script>
