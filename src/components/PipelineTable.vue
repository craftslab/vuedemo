<template>
  <v-card>
    <v-data-table
        :headers="headers"
        :items="pipelines"
        item-key="name"
        class="elevation-1"
        show-select
        sort-by="time"
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
          <v-dialog
              v-model="dialogNew"
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
                          v-model="createdItem.name"
                          label="name"
                      ></v-text-field>
                    </v-col>
                    <v-col
                        cols="12"
                        sm="6"
                        md="4"
                    >
                      <v-text-field
                          v-model="createdItem.status"
                          label="statue"
                      ></v-text-field>
                    </v-col>
                    <v-col
                        cols="12"
                        sm="6"
                        md="4"
                    >
                      <v-text-field
                          v-model="createdItem.stage"
                          label="stage"
                      ></v-text-field>
                    </v-col>
                    <v-col
                        cols="12"
                        sm="6"
                        md="4"
                    >
                      <v-text-field
                          v-model="createdItem.owner"
                          label="owner"
                      ></v-text-field>
                    </v-col>
                    <v-col
                        cols="12"
                        sm="6"
                        md="4"
                    >
                      <v-text-field
                          v-model="createdItem.time"
                          label="time"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                    color="blue darken-1"
                    text
                    @click="closeNew"
                >
                  Cancel
                </v-btn>
                <v-btn
                    color="blue darken-1"
                    text
                    @click="saveNew"
                >
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
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
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
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
export default {
  name: 'PipelineTable',
  data() {
    return {
      actions: [
        { text: 'View', icon: 'mdi-eye', click: this.viewItem },
        { text: 'Delete', icon: 'mdi-close', click: this.deleteItem }
      ],
      dialogDelete: false,
      dialogNew: false,
      dialogView: false,
      headers: [
        { text: 'Name', align: 'left', sortable: true, value: 'name' },
        { text: 'Status', align: 'center', sortable: false, value: 'status' },
        { text: 'Stage', align: 'center', sortable: false, value: 'stage' },
        { text: 'Owner', align: 'left', sortable: true, value: 'owner' },
        { text: 'Time', align: 'left', sortable: true, value: 'time' },
        { text: 'Actions', align: 'center', value: 'actions', sortable: false }
      ],
      defaultItem: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
      },
      createdIndex: -1,
      createdItem: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
      },
      pipelines: [],
      search: '',
      selected: [],
      stage: {
        build: 2,
        test: 3,
        deploy: 4
      },
      status: {
        fail: 'mdi-close-circle-outline',
        pass: 'mdi-check-circle-outline',
        running: 'mdi-rotate-left'
      },
      viewedItem: {
        name: '',
        statue: '',
        stage: '',
        owner: '',
        time: ''
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
    createItem (item) {
      this.createdIndex = this.pipelines.indexOf(item)
      this.createdItem = Object.assign({}, item)
      this.dialogNew = true
    },
    deleteItem (item) {
      this.createdIndex = this.pipelines.indexOf(item)
      this.createdItem = Object.assign({}, item)
      this.dialogDelete = true
    },
    deleteItemConfirm () {
      this.pipelines.splice(this.createdIndex, 1)
      this.closeDelete()
    },
    viewItem (item) {
      this.viewedItem = Object.assign({}, item)
      this.dialogView = true
    },
    closeNew () {
      this.dialogNew = false
      this.$nextTick(() => {
        this.createdItem = Object.assign({}, this.defaultItem)
        this.createdIndex = -1
      })
    },
    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.createdItem = Object.assign({}, this.defaultItem)
        this.createdIndex = -1
      })
    },
    closeView () {
      this.dialogView = false
    },
    saveNew () {
      if (this.createdIndex > -1) {
        Object.assign(this.pipelines[this.createdIndex], this.createdItem)
      } else {
        this.pipelines.push(this.createdItem)
      }
      this.closeNew()
    },
    getStage (item) {
      return this.stage[item.stage]
    },
    getStatus (item) {
      return this.status[item.status]
    },
  },
  watch: {
    dialogNew (val) {
      val || this.closeNew()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    },
  }
}
</script>
