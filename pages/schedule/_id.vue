<template>
  <div v-if="can(`user_access`)">
    <div class="text-center ma-2">
      <v-snackbar v-model="snackbar" top="top" color="secondary" elevation="24">
        {{ response }}
      </v-snackbar>
    </div>
    <v-row class="mt-5 mb-10">
      <v-col cols="10">
        <h3>{{ Model }}</h3>
        <div>Dashboard / {{ Model }} / Edit</div>
      </v-col>
    </v-row>
    <v-card>
      <v-card flat>
        <v-toolbar dark flat class="primary">
             <h3>Edit {{ Model }}</h3>
          </v-toolbar>
        <v-card-text>
          <v-row>
            <v-col cols="12" md="12">
              <v-text-field
                v-model="payload.shift_name"
                label="Shift Name"
              ></v-text-field>
              <span
                v-if="errors && errors.shift_name"
                class="text-danger mt-2"
                >{{ errors.shift_name[0] }}</span
              >
            </v-col>
            <v-col cols="12" md="6">
              <v-menu
                ref="time_in_menu_ref"
                v-model="time_in_menu"
                :close-on-content-click="false"
                :nudge-right="40"
                :return-value.sync="payload.time_in"
                transition="scale-transition"
                offset-y
                max-width="290px"
                min-width="290px"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="payload.time_in"
                    label="Time In"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-time-picker
                  v-if="time_in_menu"
                  v-model="payload.time_in"
                  full-width
                >
                  <v-spacer></v-spacer>
                  <v-btn
                    x-small
                    color="primary"
                    @click="time_in_menu = false"
                  >
                    Cancel
                  </v-btn>
                  <v-btn
                    x-small
                    color="primary"
                    @click="$refs.time_in_menu_ref.save(payload.time_in)"
                  >
                    OK
                  </v-btn>
                </v-time-picker>
              </v-menu>
            </v-col>
            <v-col cols="12" md="6">
              <v-menu
                ref="time_out_menu_ref"
                v-model="time_out_menu"
                :close-on-content-click="false"
                :nudge-right="40"
                :return-value.sync="payload.time_out"
                transition="scale-transition"
                offset-y
                max-width="290px"
                min-width="290px"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="payload.time_out"
                    label="Time Out"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-time-picker
                  v-if="time_out_menu"
                  v-model="payload.time_out"
                  full-width
                >
                  <v-spacer></v-spacer>
                  <v-btn
                    x-small
                    color="primary"
                    @click="time_out_menu = false"
                  >
                    Cancel
                  </v-btn>
                  <v-btn
                    x-small
                    color="primary"
                    @click="$refs.time_out_menu_ref.save(payload.time_out)"
                  >
                    OK
                  </v-btn>
                </v-time-picker>
              </v-menu>
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="payload.grace_time_in"
                label="Grace Time In (Minutes)"
                type="number"
              ></v-text-field>
              <span
                v-if="errors && errors.grace_time_in"
                class="text-danger mt-2"
                >{{ errors.grace_time_in[0] }}</span
              >
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="payload.grace_time_out"
                label="Grace Time Out (Minutes)"
                type="number"
              ></v-text-field>
              <span
                v-if="errors && errors.grace_time_out"
                class="text-danger mt-2"
                >{{ errors.grace_time_out[0] }}</span
              >
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="payload.absent_min_in"
                label="Minutes for Absent In"
                type="number"
              ></v-text-field>
              <span
                v-if="errors && errors.absent_min_in"
                class="text-danger mt-2"
                >{{ errors.absent_min_in[0] }}</span
              >
            </v-col>

            <v-col cols="12" md="6">
              <v-text-field
                v-model="payload.absent_min_out"
                label="Minutes for Absent Out"
                type="number"
              ></v-text-field>
              <span
                v-if="errors && errors.absent_min_out"
                class="text-danger mt-2"
                >{{ errors.absent_min_out[0] }}</span
              >
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12">
              <div class="text-right">
                <v-btn
                  v-if="can(`user_create`)"
                  small
                  :loading="loading"
                  color="primary"
                  @click="store_schedule"
                >
                  Submit
                </v-btn>
              </div>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-card>
  </div>
  <NoAccess v-else />
</template>

<script>
export default {
  data: () => ({
    Model: "Schedule",
    loading: false,
    time_in_menu: false,
    time_out_menu: false,
    grace_time_in_menu: false,
    grace_time_out_menu: false,

    payload: {
      shift_name: null,
      time_in: null,
      time_out: null,
      grace_time_in: null,
      grace_time_out: null,
      absent_min_in: null,
      absent_min_out: null
    },

    errors: [],
    data: [],
    response: "",
    snackbar: false
  }),
  async created() {

    this.$axios
        .get(`/schedule/${this.$route.params.id}`)
        .then(({ data }) => {
          this.loading = false;

          this.payload = data;
        })
        .catch(e => console.log(e));

  },
  methods: {
    can(per) {
      let u = this.$auth.user;
      return (
        (u && u.permissions.some(e => e.name == per || per == "/")) ||
        u.is_master
      );
    },

    store_schedule() {

      this.loading = true;

      this.$axios
        .put(`/schedule/${this.$route.params.id}`, this.payload)
        .then(({ data }) => {
          this.loading = false;

          if (!data.status) {
            this.errors = data.errors;
          } else {
            this.snackbar = true;
            this.response = "Schedule update successfully";
            setTimeout(() => this.$router.push(`/schedule`), 2000);
          }
        })
        .catch(e => console.log(e));
    }
  }
};
</script>
