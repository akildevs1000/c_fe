<template>
  <v-row class="mt-5">
    <v-col md="6">
      <h3>{{ Model }}</h3>
      <div>Dashboard / {{ Model }}</div>
    </v-col>
    <v-col cols="12">
      <Cards :columns="6" :cards="cards" />
    </v-col>
  </v-row>
</template>
<script>
export default {
  data: () => ({
    Model: "Attendance",
    cards: [
      {
        id: 1,
        heading: "Scheduler",
        subheading: "Auto Shift",
        name: "Schedule",
        icon: "mdi-history",
        link: "schedule"
      },
      {
        id: 2,
        heading: "Duty Organizer",
        subheading: "No Shift",
        name: "Duty Organizer",
        icon: "mdi-clock",
        link: "duty_organizer"
      },
      {
        id: 3,
        heading: "Summary Report",
        subheading: "Auto Shift",
        name: "Attendance Summary ",
        icon: "mdi-chart-areaspline",
        link: "attendance_summary_auto_shift"
      },
      {
        id: 3,
        heading: "Summary Report",
        subheading: "No Shift",
        name: "Attendance Summary",
        icon: "mdi-chart-areaspline",
        link: "attendance_summary_no_shift"
      },
      {
        id: 4,
        heading: "Attendance Report",
        subheading: "Auto Shift",
        name: "Attendance Report (Auto Shift)",
        icon: "mdi-chart-areaspline",
        link: "attendance_report_auto_shift"
      },
      {
        id: 5,
        heading: "Attendance Report",
        subheading: "No Shift",
        name: "Attendance Report (No Shift)",
        icon: "mdi-chart-areaspline",
        link: "attendance_report_no_shift"
      }
    ]
  })
};
</script>
