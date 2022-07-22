<template>
  <div v-if="can(`assign_permission_access`)">
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ msg }}
      </v-snackbar>
    </div>
    <v-card class="pa-5 " outlined>
      <v-row>
        <v-col cols="6">
          <v-btn fab small class="primary mb-5" to="/duty_organizer">
            <v-icon small>mdi-arrow-left</v-icon>
          </v-btn>
          <div class="display-1 pa-2">Duty Organizer</div>
        </v-col>

        <v-col cols="12">
          <v-select
            @change="runMultipleFunctions"
            v-model="department_ids"
            multiple
            chips
            x-small
            :items="departments"
            item-value="id"
            item-text="name"
            label="Department"
          ></v-select>
        </v-col>
        <!-- <v-col cols="12">
          <v-select
            @change="employeesByDesignation();"
            v-model="designation_ids"
            :items="designations"
            item-value="id"
            item-text="name"
            label="Designation"
          ></v-select>
        </v-col> -->

        <v-col cols="5">
          <v-card
            outlined
            class="pa-0"
            style="min-height:550px; max-height:550px;"
          >
            <v-toolbar flat class="primary" dark>
              Auto Shift employees
            </v-toolbar>

            <v-list-item v-if="checkIfElementsAreMoved">
              <v-checkbox
                @change="setAllAutoShift"
                :label="`Select All`"
                v-model="switchForAutoShift"
              >
              </v-checkbox>
              <v-text-field
                v-if="checkIfElementsAreMoved"
                @input="getFoundEmployees"
                v-model="searchFoundEmployees"
                label="Search Employee"
                single-line
                hide-details
                class="ml-10 mb-5"
              ></v-text-field>
            </v-list-item>
            <v-list-item-title v-if="checkIfElementsAreMoved" class="ma-5">
              {{ found_emps_count }}
            </v-list-item-title>

            <v-virtual-scroll :items="found_emps" height="300" item-height="64">
              <template v-slot:default="{ item }">
                <v-list-item :key="item.id">
                  <v-checkbox
                    :value="item"
                    v-model="as_emps"
                    :label="`${item.first_name}`"
                  >
                  </v-checkbox>
                </v-list-item>
              </template>
            </v-virtual-scroll>
            <v-list-item-title v-if="checkIfElementsAreMoved" class="ma-5">
              {{ selected_count_as }}
            </v-list-item-title>
          </v-card>
        </v-col>

        <v-col cols="2" class="text-center" style="margin-top:25%;">
          <div style="min-height:100% !important;">
            <v-btn fab @click="setEmployeeToNoShift" small class="primary">
              <v-icon small>mdi-chevron-double-right</v-icon>
            </v-btn>
            <v-btn
              fab
              @click="setEmployeeToAutoShift"
              small
              class="grey darken-3"
              dark
            >
              <v-icon small>mdi-chevron-double-left</v-icon>
            </v-btn>
          </div>
        </v-col>

        <v-col cols="5">
          <v-card
            outlined
            class="pa-0"
            style="min-height:550px; max-height:550px;"
          >
            <v-toolbar flat class="grey darken-3" dark>
              No Shift employees
            </v-toolbar>

            <v-list-item v-if="checkIfElementsAreMovedReverse">
              <v-checkbox
                @change="setAllNoShift"
                :label="`Select All`"
                v-model="switchForNoShift"
              >
              </v-checkbox>
            </v-list-item>

            <v-list-item-title
              v-if="checkIfElementsAreMovedReverse"
              class="ma-5"
            >
              {{ employees_count_no }}
            </v-list-item-title>

            <v-virtual-scroll :items="ns_emps" height="300" item-height="64">
              <template v-slot:default="{ item }">
                <v-list-item :key="item.id">
                  <v-checkbox
                    :value="item"
                    v-model="selected_employees"
                    :label="`${item.first_name}`"
                  >
                  </v-checkbox>
                </v-list-item>
              </template>
            </v-virtual-scroll>
            <v-list-item-title
              v-if="checkIfElementsAreMovedReverse"
              class="ma-5"
            >
              {{ selected_count_no }}
            </v-list-item-title>

            <v-list-item-title
              v-if="checkIfElementsAreMovedReverse"
              class="ma-5"
            >
              <span
                v-if="custom_errors && custom_errors.length"
                class="text-danger mt-2"
              >
                {{ custom_error(custom_errors[0]) }}
              </span>
              <br />
              <span
                v-if="errors && errors.employee_ids"
                class="text-danger mt-2"
              >
                {{ errors.employee_ids[0] }}
              </span>
              <br />
              <span v-if="errors && errors.company_id" class="text-danger mt-2">
                {{ errors.company_id[0] }}
              </span>
            </v-list-item-title>
          </v-card>
        </v-col>
        <v-col cols="12">
          <span v-if="errors && errors.ns_emps" class="red--text">
            {{ errors.ns_emps[0] }}
          </span>
        </v-col>
        <v-col cols="12">
          <div class="display-1 text-right">
            <v-btn
              v-if="
                can(`assign_permission_create`) &&
                  checkIfElementsAreMovedReverse
              "
              dark
              small
              color="primary"
              @click="save"
            >
              Submit
            </v-btn>
          </div>
        </v-col>
      </v-row>
    </v-card>
  </div>
  <NoAccess v-else />
</template>

<script>
export default {
  data: () => ({
    schedule_id: "",
    department_ids: [],
    designation_ids: [],
    searchFoundEmployees: null,
    employees: [], // from database
    found_emps: [], // after filter
    as_emps: [], // auto shift
    ns_emps: [], // no shift
    selected_employees: [], //selected_employees in no shift

    msg: "",
    snackbar: false,
    Rules: [v => !!v || "This field is required"],
    errors: [],
    custom_errors: [],
    shifts: [],
    departments: [],
    designations: [],
    switchForAutoShift: false,
    switchForNoShift: false,
    checkIfElementsAreMoved: true,
    checkIfElementsAreMovedReverse: false,
    options: {}
  }),
  computed: {

    selected_count_as() {
      let ids = this.as_emps;
      return `Total (${ids && ids.length}) employees selected`;
    },
    found_emps_count() {
      return `Total (${this.found_emps &&
        this.found_emps.length}) employees found`;
    },
    employees_count_no() {
      return `Total (${this.ns_emps && this.ns_emps.length}) employees found`;
    },
    selected_count_no() {
      let ids = this.selected_employees;
      return `Total (${ids && ids.length}) employees selected`;
    }
  },
  created() {
    this.options = {
      params: {
        per_page: 1000,
        company_id: this.$auth.user.company.id
      }
    };

    this.getSchedule(this.options);
    this.getDepartments(this.options);
    this.getEmployees(this.options);
  },
  methods: {
    custom_error(error) {
      return `The ${ error.split(".")[1] || "" }`
    },
    runMultipleFunctions() {
      this.employeesByDepartment();
      //  this.designationsByDepartment();
    },
    getFoundEmployees(e) {
      let employees = this.employees;
      let res = employees.filter(({ first_name: fn }) => fn.includes(e));
      this.found_emps = res;
    },
    setEmployeeToNoShift() {
      this.ns_emps = [].concat(this.as_emps, this.ns_emps);

      let found_emps = this.found_emps;
      let ids = this.as_emps.map(e => e.id);

      this.found_emps = found_emps.filter(e => !ids.includes(e.id));

      this.as_emps = [];

      if (!this.found_emps.length) {
        this.checkIfElementsAreMoved = false;
      }
      if (this.ns_emps.length) {
        this.checkIfElementsAreMovedReverse = true;
      }

      this.switchForAutoShift = false;
    },
    setEmployeeToAutoShift() {
      this.found_emps = [].concat(this.selected_employees, this.found_emps);

      let ids = this.selected_employees.map(e => e.id);

      this.ns_emps = this.ns_emps.filter(e => !ids.includes(e.id));

      this.selected_employees = this.as_emps = [];

      if (this.found_emps.length) {
        this.checkIfElementsAreMoved = true;
      }

      if (!this.ns_emps.length) {
        this.checkIfElementsAreMovedReverse = false;
      }

      this.switchForNoShift = false;
    },
    setAllAutoShift() {
      this.as_emps = this.switchForAutoShift ? this.found_emps : [];
    },
    setAllNoShift() {
      this.selected_employees = this.switchForNoShift ? this.ns_emps : [];
    },
    getSchedule(options) {
      this.$axios
        .get("schedule", options)
        .then(({ data }) => (this.shifts = data.data))
        .catch(err => console.log(err));
    },
    getDepartments(options) {
      this.$axios
        .get("departments", options)
        .then(({ data }) => {
          this.departments = [{ id: -1, name: "Select All" }].concat(data.data);
        })
        .catch(err => console.log(err));
    },
    getEmployees(options) {
      this.$axios
        .get("employee", options)
        .then(({ data }) => {
          this.employees = data.data;
          this.found_emps = this.employees;
        })
        .catch(err => console.log(err));
    },
    employeesByDepartment() {
      // this.found_emps = this.found_emps.filter(e => ids.includes(e.department_id));

      if (!this.department_ids.length) {
        this.found_emps = this.employees = [];
        return;
      }

      this.options.params.department_ids = this.department_ids;
      this.$axios
        .get(`employeesByDepartment`, this.options)
        .then(({ data }) => {
          this.employees = data;
          this.found_emps = this.employees;
        })
        .catch(err => console.log(err));
    },
    employeesByDesignation() {
      this.$axios
        .get(`employeesByDesignation/${this.designation_ids}`, this.options)
        .then(({ data }) => {
          this.employees = data;
          this.found_emps = this.employees;
        })
        .catch(err => console.log(err));
    },
    designationsByDepartment() {
      this.$axios
        .get(`designationsByDepartment/${this.department_ids}`, this.options)
        .then(({ data }) => {
          this.designations = [{ id: "0", name: "Select All" }].concat(data);
        })
        .catch(err => console.log(err));
    },

    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },

    searchIt(key) {
      if (key.length == 0) {
        this.getPermissions();
      } else if (key.length > 2) {
        this.getPermissions(`permission/search/${key}`);
      }
      this.ns_emps = [];
    },
    save() {
      this.errors = [];
      let payload = {
        employee_ids: this.selected_employees.map(e => e.employee_id),
        company_id: this.$auth.user.company.id
      };
      this.$axios
        .post("no-shift", payload)
        .then(({ data }) => {
          if (!data.status) {
            if(data?.custom_errors){
              this.custom_errors = data.custom_errors;
              this.errors = [];

            }
            if(data?.errors){
              this.errors = data.errors;
              this.custom_errors = [];
            }
            return;
          }
          this.msg = data.message;
          this.snackbar = true;
          setTimeout(() => this.$router.push("/duty_organizer"), 2000);
        })
        .catch(err => console.log(err));
    }
  }
};
</script>
