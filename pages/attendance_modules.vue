<template>
  <div>
    <Cards :cards="cards" />
  </div>
</template>
<script>
export default {
  data: () => ({
    cards: [
      { id: 1, name: "Schedule", icon: "mdi-history", link: "schedule" },
      { id: 2, name: "Duty Organizer", icon: "mdi-clock", link: "duty_organizer" },
      { id: 2, name: "Attendance Report", icon: "mdi-chart-areaspline", link: "attendance_report" },
    ]
  })
};
</script>
