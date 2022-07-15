<template>
  <v-app>
    <v-navigation-drawer
      v-model="drawer"
      dark
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      class="no_print"
      color="background"
    >
      <v-list v-for="(i, idx) in items" :key="idx" style="padding: 5px 0 0 0px">
        <v-list-item
          style="min-height: 0"
          :to="i.to"
          router
          v-if="!i.hasChildren"
        >
          <v-list-item-icon v-if="i.permission" class="ma-2">
            <v-icon>{{ i.icon }}</v-icon>
          </v-list-item-icon>
          <v-list-item-title v-if="i.permission">
            {{ i.title }}&nbsp;
            <v-badge
              v-if="i.title == 'Orders' && order_count > 0"
              color="primary"
              :content="order_count"
              small
            />
          </v-list-item-title>
        </v-list-item>

        <v-list-item
          v-else
          style="min-height: 0"
          @click="i.open_menu = !i.open_menu"
        >
          <v-list-item-icon v-if="i.permission" class="ma-2">
            <v-icon>{{ i.icon }}</v-icon>
          </v-list-item-icon>
          <v-list-item-title v-if="i.permission">{{
            i.title
          }}</v-list-item-title>
          <v-icon v-if="i.permission" small>{{
            !i.open_menu ? "mdi-chevron-down" : "mdi-chevron-up"
          }}</v-icon>
        </v-list-item>
         <div v-if="i.open_menu && i.title == `Modules`">
          <div
            style="margin-left: 50px"
            v-for="(j, jdx) in modules.module_names"
            :key="jdx"
          >
            <v-list-item v-if="j.permission" style="min-height: 0" :to="j.to">
              <v-list-item-title >{{ j.title }}</v-list-item-title>

              <v-list-item-icon>
                <v-icon :to="j.to">{{j.icon}}</v-icon>
              </v-list-item-icon>
            </v-list-item>
          </div>
        </div>

         <div v-else-if="i.open_menu && i.title != `Modules`">
          <div
            style="margin-left: 50px"
            v-for="(j, jdx) in i.hasChildren"
            :key="jdx"
          >
            <v-list-item v-if="j.permission" style="min-height: 0" :to="j.to">


              <v-list-item-title>{{ j.title }}</v-list-item-title>

              <v-list-item-icon>
                <v-icon :to="i.to">{{ j.icon }}</v-icon>
              </v-list-item-icon>
            </v-list-item>
          </div>
        </div>

      </v-list>
    </v-navigation-drawer>
    <!-- color="#910105 #fd9d00" -->

    <v-app-bar color="gradient-custom-2" dark :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      {{ title }}
      <v-spacer />
      <span v-if="this.$auth.user">
        Welcome, <b>{{ this.$auth.user.name }}</b
        >&nbsp;
        <v-icon small @click="logout"> {{ logout_btn.icon }} </v-icon>
      </span>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>

    <v-footer :fixed="fixed" app>
      <v-icon Logout color="primary" @click="logout">{{
        logout_btn.icon
      }}</v-icon>

      <span class="primary--text">Logout</span>

      <v-spacer></v-spacer>
      <span class="primary--text">
        <v-icon v-if="$auth && this.$auth.user.role" Logout color="primary"
          >mdi-account</v-icon
        >
      </span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  mounted() {},
  data() {
    return {
      year: new Date().getFullYear(),
      clipped: false,
      open_menu: [],
      drawer: true,
      fixed: false,
      order_count: "",
      admins: [
        ["Management", "mdi-account-multiple-outline"],
        ["Settings", "mdi-cog-outline"]
      ],
      cruds: [
        ["Create", "mdi-plus-outline"],
        ["Read", "mdi-file-outline"],
        ["Update", "mdi-update"],
        ["Delete", "mdi-delete"]
      ],

      items: [
        {
          icon: "mdi-home",
          title: "Home",
          to: "/",
          permission: this.can("/")
        },
        {
          icon: "mdi-domain",
          title: `My Company`,
          open_menu: true,
          permission: this.can("company_access"),
          hasChildren: [
            {
              icon: "mdi-domain",
              title: "Company Info",
              to: `/companies/details/${this.$auth?.user?.company?.id}`,
              permission: this.can("company_access")
            },
            {
              icon: "mdi-account",
              title: "Employees",
              to: "/employees",
              permission: this.can("employee_access")
            },
            {
              icon: "mdi-door",
              title: "Department",
              to: "/department",
              permission: this.can("department_access")
            },
            {
              icon: "mdi-door",
              title: "Designation",
              to: "/designation",
              permission: this.can("designation_access")
            },

            // employees
          ]
        },
        {
          icon: "mdi-apps",
          title: "Modules",
          open_menu: true,
          permission: this.can("module_access"),
          hasChildren: [
            {
              icon: "mdi-chart-bubble",
              title: "Payroll",
              to: "/payroll_modules",
              permission: this.can("module_access")
            },
            {
              icon: "mdi-chart-bubble",
              title: "Attendance",
              to: "/attendance_modules",
              permission: this.can("module_access")
            },
            {
              icon: "mdi-chart-bubble",
              title: "HR Management",
              to: "/hr_modules",
              permission: this.can("module_access")
            }
          ]
        },
        {
          icon: "mdi-account",
          title: "User Management",
          open_menu: true,
          permission: this.can("user_access"),
          hasChildren: [
            {
              icon: "mdi-account",
              title: "Users",
              to: "/user",
              permission: this.can("user_access")
            },
            {
              icon: "mdi-account",
              title: "Roles",
              to: "/role",
              permission: this.can("role_access")
            },
            {
              icon: "mdi-lock",
              title: "Assign Permissions",
              to: "/assign_permission",
              permission: this.can("assign_permission_access")
            }
          ]
        },
        {
          icon: "mdi-history",
          title: "Attendance Logs",
          to: "/attendance_logs",
          permission: this.can("/")
        },
      ],
      modules : {
        module_ids : [],
        module_names : []
      },
      clipped: true,

      miniVariant: false,
      title: "Attendance System",
      logout_btn: {
        icon: "mdi-logout",
        label: "Logout"
      }
    };
  },
  created() {
    this.getCompanyDetails();
  },
  methods: {

    caps(str) {
      return str.replace(/\b\w/g, c => c.toUpperCase());
    },

    getCompanyDetails() {
      let user = this.$auth.user;

      this.$axios.get(`company/${user?.company?.id}`).then(({ data }) => {

        let { modules } = data.record;

        this.modules = {
          module_ids : modules.module_ids,
          module_names : modules.module_names.map(e => ({
              icon: "mdi-chart-bubble",
              title: this.caps(e),
              to: "/" + e +"_modules",
              permission: true
            })),
        };
      });
    },
    can(per) {
      let user = this.$auth.user;
      return (
        (user && user.permissions.some(e => e.name == per || per == "/")) ||
        user.is_master
      );
    },
    async logout() {
      await this.$auth.logout();
    }
  },
  computed: {}
};
</script>
<style>
.gradient-custom-2 {
  background: #fccb90;
  background: -webkit-linear-gradient(to right, #ee7724, #d8363a);
  background: linear-gradient(to right, #ee7724, #d8363a);
}
/* .page-enter-active,
.page-leave-active {
  transition: opacity 0.5s;
}
.page-enter,
.page-leave-to {
  opacity: 0;
}

.layout-enter-active,
.layout-leave-active {
  transition: opacity 0.5s;
}
.layout-enter,
.layout-leave-to {
  opacity: 0;
}

.slide-bottom-enter-active,
.slide-bottom-leave-active {
  transition: opacity 0.25s ease-in-out, transform 0.25s ease-in-out;
}
.slide-bottom-enter,
.slide-bottom-leave-to {
  opacity: 0;
  transform: translate3d(0, 15px, 0);
}
.bounce-enter-active {
  transform-origin: top;
  animation: bounce-in 0.8s;
}
.bounce-leave-active {
  transform-origin: top;
  animation: bounce-out 0.5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(0);
  }
} */
</style>
