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
      { id: 2, name: "Attendance Report", icon: "mdi-chart-areaspline", link: "attendance_report" },
    ]
  })
};
</script>
