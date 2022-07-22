<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-row>
          <v-col
            md="6"
            v-for="(i, index) in items"
            :key="index"
          >
            <v-card class="no_print">
              <v-list-item three-line>
                <v-list-item-content>
                  <div class="text-overline mb-4">
                    {{ i.title }}
                  </div>
                  <v-list-item-title class="text-h5 mb-1">
                    {{ i.value }}
                  </v-list-item-title>
                </v-list-item-content>

                <v-list-item-avatar
                  size="60"
                  style="background: linear-gradient(to right, #ee7724, #d8363a);"
                >
                  <v-icon dark>{{ i.icon }}</v-icon>
                </v-list-item-avatar>
              </v-list-item>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <AttendanceLogs />
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [],
      headers: [
        { text: "UserID", value: "UserID" },
        { text: "LogTime", value: "LogTime" },
        { text: "DeviceID", value: "DeviceID" },
        { text: "SerialNumber", value: "SerialNumber" }
      ],
      data: []
    };
  },
  created() {
    this.initialize();
  },
  methods: {
    async initialize() {
      let options = {
        company_id: this.$auth.user.company.id
      };
      this.$axios.get(`count`, { params: options }).then(({ data }) => {
        this.items = data;
      });
    }
  }
};
</script>
