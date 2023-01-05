
<template>
  <v-data-table v-model="selected1" :headers="headers" :items="desserts" :single-select="singleSelect1" item-key="id"
      show-select class="elevation-1" >
    <!-- <template v-slot:item="{ item }">
      <tr>
        <td class="custom">{{ item.model }}</td>
        <td class="custom">{{ item.part_number }}</td>
        <td class="custom">{{ item.project_type }}</td>
        <td class="custom">{{ item.quantity }}</td>
        <td class="custom">{{ item.priority }}</td>
        <td class="custom">{{ item.status_p }}</td>
        <td class="custom">{{ item.start_p }}</td>
        <td class="custom">{{ item.end_p }}</td>
        <td class="custom">{{ item.days }}</td>
        <td class="custom">{{ item.completed }}</td>
        <td class="custom">{{ item.kicks }}</td>
        <td class="custom">{{ item.drawing }}</td>
        <td class="custom">{{ item.steel }}</td>
        <td class="custom">{{ item.die }}</td>
        <td class="custom">{{ item.jigs }}</td>
        <td class="custom">{{ item.broach }}</td>
        <td class="custom">{{ item.gauges }}</td>
        <td class="custom">{{ item.forging }}</td>
        <td class="custom">{{ item.soft }}</td>
        <td class="custom">{{ item.heat }}</td>
        <td class="custom">{{ item.qg }}</td>
      </tr>
    </template> -->
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
            <v-btn color="primary" dark class="mb-2 mr-2" v-bind="attrs" v-on="on" @click="edit(selected1)" >
              <v-icon small class="mr-1" >mdi-pencil</v-icon> EDIT DATA
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
                    <v-text-field v-model="postdata.model" label="Model"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="postdata.part_number" type="number" label="Part Number"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">

                    <v-autocomplete v-model="postdata.project_type" :items="type" label="Project Type"
                      placeholder="Select...">
                    </v-autocomplete>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="postdata.quantity" type="number" label="Quantity"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="postdata.priority" :items="priority_type" label="Priority"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>


                </v-row>

                <!-- STATUS FOR PLAN  -->
                <v-row>

                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="postdata.status_p" :items="status_p" label="Status"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.start_p" label="Start Date " prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.start_p" :active-picker.sync="activePicker"
                        :min="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"></v-date-picker>
                    </v-menu>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.end_p" label="End Date " prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.end_p" :active-picker.sync="activePicker"
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
                        <v-text-field v-model="postdata.drawing" label="Drawing Approval from customer"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.drawing" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>

                </v-row>

                <!-- STEEL  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.steel" label="Steel master release" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.steel" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- DIE DESIGN  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.die" label="Die Design & Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.die" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- Jigs Design & drawing release -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.jigs" label="Jigs Desiign and Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.jigs" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- BROACH  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.broach" label="BROACH Manufacturing" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
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
                        <v-text-field v-model="postdata.gauges" label="GUAGES Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.gauges" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- FORGING  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.forging" label="Forging" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.forging" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- SOFT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.soft" label="Soft Machining" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.soft" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- HEAT TREATMENT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.heat" label="Heat Treatment" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.heat" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>
                  </v-col>
                </v-row>

                <!-- QG4 -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="postdata.qg" label="QG4" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="postdata.qg" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>



              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog=false">
                Cancel
              </v-btn>
              <v-btn color="blue darken-1" text @click="save()">
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- dialogue 2  -->

        <v-dialog v-model="dialog1">
          
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editdata.model" label="Model"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editdata.part_number" type="number" label="Part Number"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">

                    <v-autocomplete v-model="editdata.project_type" :items="type" label="Project Type"
                      placeholder="Select...">
                    </v-autocomplete>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editdata.quantity" type="number" label="Quantity"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="editdata.priority" :items="priority_type" label="Priority"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>


                </v-row>

                <!-- STATUS FOR PLAN  -->
                <v-row>

                  <v-col cols="12" sm="6" md="4">
                    <v-autocomplete v-model="editdata.status_p" :items="status_p" label="Status"
                      placeholder="Select...">
                    </v-autocomplete>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.start_p" label="Start Date " prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.start_p" :active-picker.sync="activePicker"
                        :min="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"></v-date-picker>
                    </v-menu>

                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.end_p" label="End Date " prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.end_p" :active-picker.sync="activePicker"
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
                        <v-text-field v-model="editdata.drawing" label="Drawing Approval from customer"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.drawing" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>

                </v-row>

                <!-- STEEL  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.steel" label="Steel master release" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.steel" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- DIE DESIGN  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.die" label="Die Design & Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.die" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- Jigs Design & drawing release -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.jigs" label="Jigs Desiign and Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.jigs" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- BROACH  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.broach" label="BROACH Manufacturing" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.broach" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- GUAGES  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.gauges" label="GUAGES Drawing Release"
                          prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.gauges" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>

                  </v-col>
                </v-row>

                <!-- FORGING  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.forging" label="Forging" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.forging" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- SOFT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.soft" label="Soft Machining" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.soft" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>

                <!-- HEAT TREATMENT  -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.heat" label="Heat Treatment" prepend-icon="mdi-calendar"
                          readonly v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.heat" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>
                  </v-col>
                </v-row>

                <!-- QG4 -->

                <v-row>
                  <v-col cols="12" sm="6" md="4">

                    <v-menu ref="menu" v-model="menu" :close-on-content-click="false" transition="scale-transition"
                      offset-y min-width="auto">
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field v-model="editdata.qg" label="QG4" prepend-icon="mdi-calendar" readonly
                          v-bind="attrs" v-on="on"></v-text-field>
                      </template>
                      <v-date-picker v-model="editdata.qg" :active-picker.sync="activePicker"></v-date-picker>
                    </v-menu>


                  </v-col>
                </v-row>



              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog=false">
                Cancel
              </v-btn>
              <v-btn color="blue darken-1" text @click="update()">
                UPDATE
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <!-- <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog> -->
      </v-toolbar>
    </template>
    <!-- <template v-slot:[`item.actions`]="{ item }">
      <v-icon small class="mr-2" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon small @click="deleteItem(item)" style="visibility: visible;" id="A">
        mdi-delete
      </v-icon>
    </template> -->




  </v-data-table>
</template>

<script>
import axios from "axios"

export default {


  data: () => ({

    select: { state: "" },
    selected1: [],
    singleSelect1: true,

    search: '',
    status_p: ['Plan', 'Actual'],
    status_a: ['Actual'],
    status_r: ['Remark'],
    priority_type: ['H', 'M', 'S'],
    type: ['A', 'B', 'C', 'D'],
    dialog: false,
    dialogDelete: false,

    desserts: [],
    editedIndex: -1,
    postdata: {
      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: '',
      start_p: '',
      end_p: '',
      days: '',
      completed: '',
      kicks: '',
      drawing: '',
      steel: '',
      die: '',
      jigs: '',
      broach: '',
      gauges: '',
      forging: '',
      soft: '',
      heat: '',
      qg: '',
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
      days: '',
      completed: '',
      kicks: '',
      drawing: '',
      steel: '',
      die: '',
      jigs: '',
      broach: '',
      gauges: '',
      forging: '',
      soft: '',
      heat: '',
      qg: '',

    },
    editedItem: {
      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: '',
      start_p: 'Actual',
      end_p: '',
      days: '',
      completed: '',
      kicks: '',
      drawing: '',
      steel: '',
      die: '',
      jigs: '',
      broach: '',
      gauges: '',
      forging: '',
      soft: '',
      heat: '',
      qg: '',
    },
    editdata: {
      model: '',
      part_number: '',
      project_type: '',
      quantity: '',
      priority: '',
      status_p: '',
      start_p: '',
      end_p: '',
      days: '',
      completed: '',
      kicks: '',
      drawing: '',
      steel: '',
      die: '',
      jigs: '',
      broach: '',
      gauges: '',
      forging: '',
      soft: '',
      heat: '',
      qg: '',
      },

  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'ADD DATA' : 'UPDATE DATA '
    },

    headers() {
      return [
        { text: 'Model', value: 'model', sortable: false, class: "table_border", width: "40%" },
        { text: 'Part No.', value: 'part_number', sortable: false, class: "table_border" },
        { text: 'Project Type', value: 'project_type', sortable: false, class: "table_border" },
        { text: 'Quantity', value: 'quantity', sortable: false, class: "table_border" },
        { text: 'Priority', value: 'priority', sortable: false, class: "table_border" },
        { text: 'Status', value: 'status_p', sortable: false, class: "table_border" },
        { text: 'Start Date', value: 'start_p', sortable: false, class: "table_border" },
        { text: 'End Date', value: 'end_p', sortable: false, class: "table_border" },
        { text: 'Number of Days', value: 'days', sortable: false, class: "table_border" },
        { text: '%Completed', value: 'completed', sortable: false, class: "table_border" },
        { text: 'Kick off', value: 'kicks', sortable: false, class: "table_border" },
        { text: 'Drawing Approval From Customer', value: 'drawing', sortable: false, class: "table_border" },
        { text: 'Steel Master Release', value: 'steel', sortable: false, class: "table_border" },
        { text: 'Die Design & Drawing Release', value: 'die', sortable: false, class: "table_border" },
        { text: 'Jigs Design & drawing release', value: 'jigs', sortable: false, class: "table_border" },
        { text: 'BROACH  manufacturing', value: 'broach', sortable: false, class: "table_border" },
        { text: 'Gauges Drawing Release', value: 'gauges', sortable: false, class: "table_border" },
        { text: 'Forging(QG-1)', value: 'forging', sortable: false, class: "table_border" },
        { text: 'Soft Machining', value: 'soft', sortable: false, class: "table_border" },
        { text: 'Heat Treatment', value: 'heat', sortable: false, class: "table_border" },
        { text: 'QG4', value: 'qg', sortable: false, class: "table_border" },
        // { text: 'Actions', value: 'actions', sortable: false ,class:"table_border"}
      ]
    },
  },

  mounted() {

    this.getapicall();

  },

  methods: {
    async getapicall() {


      console.log("previous postdata : ", this.postdata)

      await axios
        .get("http://localhost:3000/database1")
        .then((resp) => {
          this.desserts = resp.data;

          console.log("this.desserts : ", this.desserts)
        })
        .catch((err) => {
          console.log("Error : ", err);
        });



    },
    async getapicall2() {


      console.log("previous postdata : ", this.postdata)

      await axios
        .get("http://localhost:3000/database1")
        .then((resp) => {
          this.desserts = resp.data;

          console.log("this.desserts : ", this.desserts)
        })
        .catch((err) => {
          console.log("Error : ", err);
        });

    },

    async save() {

      this.dialog = false,

        console.log("UPdated postdata : ", this.postdata.model)

      await axios
        .post("http://localhost:3000/database1", this.postdata)
        .then((result) => {
          console.log("Result : ", result);
          this.getapicall();
          this.postdata = {}
          console.log("data inserted : ", this.desserts);
        })
        .catch((err) => {
          console.log("Error : ", err);
        });


      // let r = JSON.stringify(this.desserts)
      // let parsed = JSON.parse(r);
      // console.log("Parsed : ", parsed)




    },

    edit(inputd) {

this.editdata = inputd[0];
this.dialog1 = true;
console.log("input selected : ", this.editdata.id);
console.log("editdata selected : ", this.editdata);
},

async update() {
this.dialog1 = false;
console.log("edited data in update : ", this.editdata)

await axios.put("http://localhost:3000/DATA1/" + this.editdata.id, this.editdata).then((resp) => {
  console.log(resp);
  this.getapicall();
}).catch((err) => {
  console.log("Error : ", err);
})

}


  },

  // watch: {
  //   dialog(val) {
  //     val || this.close()
  //   },
  //   dialogDelete(val) {
  //     val || this.closeDelete()
  //   },
  // },

}
</script>

<style>
/* .table_border {
  border: 1px solid black;
  background-color: azure;

} */

.custom {
  border: 1px solid black;
}
</style>


    <!-- ********** REQUIRED METHODS **************** -->



  <!-- // created() {
  //   this.initialize()
  // },

  // methods: {
  //   initialize() {


  //     //  this.desserts = table.data

  //     // Hard Coded Data 
  //     this.desserts = []
  //   },

  //   editItem(item) {
  //     this.editedIndex = this.desserts.indexOf(item)
  //     // this.editedItem = Object.assign({}, item)
  //     this.dialog = true
  //   },

  //   deleteItem(item) {
  //     this.editedIndex = this.desserts.indexOf(item)
  //     this.editedItem = Object.assign({}, item)
  //     // this.editedIndex = this.desserts.indexOf(item)
  //     // this.editedItem = Object.assign({}, item)

  //     this.dialogDelete = true
  //   },

  //   deleteItemConfirm() {
  //     this.desserts.splice(this.editedIndex, 1)
  //     this.desserts.splice(this.editedIndex, 1)

  //     this.closeDelete()
  //   },

  //   close() {
  //     this.dialog = false
  //     this.$nextTick(() => {
  //       this.editedItem = Object.assign({}, this.defaultItem)
  //       this.editedIndex = -1
  //     })
  //   },

  //   closeDelete() {
  //     this.dialogDelete = false
  //     this.$nextTick(() => {
  //       this.editedItem = Object.assign({}, this.defaultItem)
  //       this.editedIndex = -1
  //     })
  //   },

  //   // save() {
  //   //   if (this.editedIndex > -1) {
  //   //     Object.assign(this.desserts[this.editedIndex], this.editedItem)

  //   //   }
  //   //   else {
  //   //     this.desserts.push(this.editedItem)
  //   //     this.desserts.push(this.editedItem2)

  //   //   }
  //   //   this.close()
  //   // },
  // }, -->
