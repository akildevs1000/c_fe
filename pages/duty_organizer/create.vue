<template>
  <div v-if="can(`assign_permission_access`)">
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ msg }}
      </v-snackbar>
    </div>

    <v-card>
      <v-form ref="form" lazy-validation>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col>
                <div class="display-1 pa-2">Duty Organizer</div>
              </v-col>
              <v-col>
                <div class="display-1 pa-2 text-right">
                  <v-btn small class="primary" to="/assign_permission">
                    <v-icon small>mdi-arrow-left</v-icon>&nbsp;Back
                  </v-btn>
                </div>
              </v-col>
              <v-col cols="12">
                <v-select
                  :rules="Rules"
                  v-model="schedule_id"
                  :items="shifts"
                  item-value="id"
                  item-text="shift_name"
                  label="Shift*"
                ></v-select>
                <span v-if="errors && errors.schedule_id" class="red--text">
                  {{ errors.schedule_id[0] }}
                </span>


                <!-- <v-select
                  :rules="Rules"
                  v-model="department_id"
                  :items="departments"
                  item-value="id"
                  item-text="name"
                  label="Department*"
                ></v-select> -->
                <span v-if="errors && errors.department_id" class="red--text">
                  {{ errors.department_id[0] }}
                </span>

                Total ({{employee_ids && employee_ids.length}}) employees selected

                <v-checkbox
                  @change="setAllIds"
                  :label="`Select All`"
                  v-model="just_ids"
                >
                </v-checkbox>
                <v-checkbox
                  v-for="(employee, idx) in employees"
                  :key="idx"
                  :value="employee.id"
                  v-model="employee_ids"
                  :label="`${employee.first_name}`"
                >
                </v-checkbox>
              </v-col>
              <v-col cols="12">
                <span v-if="errors && errors.employee_ids" class="red--text">
                  {{ errors.employee_ids[0] }}
                </span>
              </v-col>

              <v-col>
                <v-btn
                  v-if="can(`assign_permission_create`)"
                  dark
                  small
                  color="primary"
                  class="mr-4"
                  @click="save"
                >
                  Submit
                </v-btn>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
      </v-form>

      <template v-slot:item.action="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
    </v-card>
  </div>
  <NoAccess v-else />
</template>

<script>
export default {
  data: () => ({
    schedule_id: "",
    department_id: "",
    search: "",
    employee_ids: [],
    employees: [],
    msg: "",
    snackbar: false,
    Rules: [v => !!v || "This field is required"],
    errors: [],
    shifts: [],
    departments:[],
    just_ids: false
  }),
  created() {

    let options = {
        params: {
          per_page: 100,
          company_id: this.$auth.user.company.id
        }
      };

    this.$axios
      .get("schedule", options)
      .then(({ data }) => (this.shifts = data.data))
      .catch(err => console.log(err));

      this.$axios
      .get("departments", options)
      .then(({ data }) => (this.departments = data.data))
      .catch(err => console.log(err));

      this.$axios
      .get("employee", options)
      .then(({ data }) => (this.employees = data.data))
      .catch(err => console.log(err));

  },
  methods: {
    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },

    setAllIds() {
      this.employee_ids = this.just_ids
        ? this.employees.map(e => e.id)
        : [];
    },
    searchIt(key) {
      if (key.length == 0) {
        this.getPermissions();
      } else if (key.length > 2) {
        this.getPermissions(`permission/search/${key}`);
      }
      this.employee_ids = [];
    },
    save() {
      this.errors = [];
      let payload = {
        schedule_id: this.schedule_id,
        employee_ids: this.employee_ids,
        company_id: this.$auth.user.company.id
      };
      console.log("🚀 ~ file: create.vue ~ line 107 ~ save ~ payload", payload);
      this.$axios.post("assign-permission", payload).then(({ data }) => {
        if (!data.status) {
          this.errors = data.errors;
          return;
        }
        this.msg = "Permissions has been assigned";
        this.snackbar = true;
        setTimeout(() => this.$router.push("/assign_permission"), 2000);
      });
    }
  }
};
</script>
