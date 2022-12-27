<template>
  <v-data-table :headers="headers" :items="desserts" :search="search" class="elevation-1 ">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>NPD PROJECT STATUS SHEET </v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
              ADD DATA
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.model" label="Model"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.part_number" label="Part Number"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">

                    <v-autocomplete v-model="editedItem.project_type" :items="type" label="Project Type"
                      placeholder="Select...">
                    </v-autocomplete>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.quantity" label="Quantity"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="editedItem.priority" :items="priority_type" label="Priority"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>


                </v-row>

                <!-- STATUS FOR PLAN  -->
                <v-row>

                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="editedItem.status_p" :items="status_p" label="Status"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.start_p" label="Start Date " prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.start_p" :active-picker.sync="activePicker"
                        :min="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"></v-date-picker>
                    </v-menu>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.end_p" label="End Date " prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.end_p" :active-picker.sync="activePicker"
                        :min="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"></v-date-picker>
                    </v-menu>

                  </v-col>

                </v-row>


                <!-- DRAWING  -->


                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.drawing" label="Drawing Approval from customer"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.drawing" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>

                </v-row>

                <!-- STEEL  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.steel" label="Steel master release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.steel" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- DIE DESIGN  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.die" label="Die Design & Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.die" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- Jigs Design & drawing release -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.jigs" label="Jigs Desiign and Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.jigs" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- BROACH  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.broach" label="BROACH Manufacturing"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.broach" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- GUAGES  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.gauges" label="GUAGES Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.gauges" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- FORGING  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.forging" label="Forging" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.forging" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- SOFT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.soft" label="Soft Machining" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.soft" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- HEAT TREATMENT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.heat" label="Heat Treatment" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.heat" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>
                  </v-col>
                </v-row>

                <!-- QG4 -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editedItem.qg" label="QG4" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editedItem.qg" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>



              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">
                Cancel
              </v-btn>
              <v-btn color="blue darken-1" text @click="save">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:[`item.actions`]="{ item }">
      <v-icon small class="mr-2" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon small @click="deleteItem(item)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
// import table from './tracking.json'

export default {

  data: () => ({

    counter_p: 0,

    search: '',
    status_p: ['Plan'],
    status_a: ['Actual'],
    status_r: ['Remark'],
    priority_type: ['H', 'M', 'S'],
    type: ['A', 'B', 'C', 'D'],
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: 'Model', value: 'model', sortable: false, borderLeft: 'thick solid hsl(0, 100%, 50%)' },
      { text: 'Part No.', value: 'part_number', sortable: false },
      { text: 'Project Type', value: 'project_type', sortable: false },
      { text: 'Quantity', value: 'quantity', sortable: false },
      { text: 'Priority', value: 'priority', sortable: false },
      { text: 'Status', value: 'status_p', sortable: false },
      { text: 'Start Date', value: 'start_p', sortable: false },
      { text: 'End Date', value: 'end_p', sortable: false },
      { text: 'Number of Days', value: 'days', sortable: false },
      { text: '%Completed', value: 'completed', sortable: false },
      { text: 'Kick off', value: 'kicks', sortable: false },
      { text: 'Drawing Approval From Customer', value: 'drawing', sortable: false },
      { text: 'Steel Master Release', value: 'steel', sortable: false },
      { text: 'Die Design & Drawing Release', value: 'die', sortable: false },
      { text: 'Jigs Design & drawing release', value: 'jigs', sortable: false },
      { text: 'BROACH  manufacturing', value: 'broach', sortable: false },
      { text: 'Gauges Drawing Release', value: 'gauges', sortable: false },
      { text: 'Forging(QG-1)', value: 'forging', sortable: false },
      { text: 'Soft Machining', value: 'soft', sortable: false },
      { text: 'Heat Treatment', value: 'heat', sortable: false },
      { text: 'QG4', value: 'qg', sortable: false },
      { text: 'Actions', value: 'actions', sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: '',
      start_p: '',
      end_p: '',
      days: 0,
      completed: 0,
      kicks: 0,
      drawing: 0,
      steel: 0,
      die: 0,
      jigs: 0,
      broach: 0,
      gauges: 0,
      forging: 0,
      soft: 0,
      heat: 0,
      qg: 0,
    },
    editedItem2: {
      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: 'Actual',
      start_p: '',
      end_p: '',
      days: 0,
      completed: 0,
      kicks: 0,
      drawing: 0,
      steel: 0,
      die: 0,
      jigs: 0,
      broach: 0,
      gauges: 0,
      forging: 0,
      soft: 0,
      heat: 0,
      qg: 0,
    },
    defaultItem: {

      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: '',
      start_p: '',
      end_p: '',
      days: 0,
      completed: 0,
      kicks: 0,
      drawing: 0,
      steel: 0,
      die: 0,
      jigs: 0,
      broach: 0,
      gauges: 0,
      forging: 0,
      soft: 0,
      heat: 0,
      qg: 0,




    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ADD DATA' : 'UPDATE DATA '
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {


      //  this.desserts = table.data


      this.desserts = [

        {

          model: 'AM13A3',
          part_number: '',
          project_type: 'A',
          quantity: 1500,
          priority: 'M',
          status_p: 'plan',
          start_p: '',
          end_p: '',
          days: 0,
          completed: 0,
          kicks: 0,
          drawing: '2022-12-22',
          steel: '2022-12-26',
          die: 0,

          broach: 0,
          gauges: 0,
          forging: 0,
          soft: 0,
          heat: 0,
          qg: 0,

        },

        {
          model: '',
          part_number: '',
          project_type: '',
          quantity: '',
          priority: '',
          status_p: 'Actual',
          start_p: '',
          end_p: '',
          days: 0,
          completed: 0,
          kicks: 0,
          drawing: '',
          steel: '',
          die: 0,
          broach: 0,
          gauges: 0,
          forging: '',
          soft: '',
          heat: '',
          qg: '',

        },


        {
          model: 'AM16A3',
          part_number: '',
          project_type: 'B',
          quantity: 209,
          priority: 'M',
          status_p: 'plan',
          start_p: '2022-10-30',
          end_p: '2022-11-30',
          days: 47,
          completed: 75,
          kicks: 0,
          drawing: '',
          steel: '',
          die: 0,
          broach: 0,
          gauges: 0,
          forging: '23/10/22',
          soft: '2022-11-08',
          heat: '2022-11-24',
          qg: '2022-11-30',

        },

        {
          model: '',
          part_number: '',
          project_type: '',
          quantity: '',
          priority: '',
          status_p: 'Actual',
          start_p: '',
          end_p: '',
          days: 0,
          completed: 0,
          kicks: 0,
          drawing: '',
          steel: '',
          die: 0,
          broach: 0,
          gauges: 0,
          forging: '',
          soft: '',
          heat: '',
          qg: '',

        },

        {
          model: 'AM05L1',
          part_number: '',
          project_type: 'A',
          quantity: 500,
          priority: 'M',
          status_p: 'plan',
          start_p: '2022-12-02',
          end_p: '2023-01-19',
          days: 29,
          completed: 0,
          kicks: 0,
          drawing: '',
          steel: '',
          die: 0,
          broach: 0,
          gauges: 0,
          forging: '',
          soft: '',
          heat: '',
          qg: '',

        },

        {
          model: '',
          part_number: '',
          project_type: '',
          quantity: '',
          priority: '',
          status_p: 'Actual',
          start_p: '',
          end_p: '',
          days: 0,
          completed: 0,
          kicks: 0,
          drawing: '',
          steel: '',
          die: 0,
          broach: 0,
          gauges: 0,
          forging: '',
          soft: '',
          heat: '',
          qg: '',

        },




      ]
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.desserts.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem)

      } else {
        this.desserts.push(this.editedItem)
        this.desserts.push(this.editedItem2)

      }
      this.close()
    },
  },
}
</script>
