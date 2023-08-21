<script setup>

</script>

<template>
<div class="home-container">
<n-tabs type="line" placement="left" class="tabs">
  <n-tab-pane name="Mower-基础设置"><mowersetting /></n-tab-pane>
  <n-tab-pane name="Mower-基建设置"><basementsetting /></n-tab-pane>
  <n-tab-pane name="Mower-线索设置"><clue /></n-tab-pane>
  <n-tab-pane name="Mower-邮件设置"><email /></n-tab-pane>
  <n-tab-pane name="MAA-基础设置"><maa-basic /></n-tab-pane>
  <n-tab-pane name="MAA-肉鸽/保全/生息"><Maahugmission /></n-tab-pane>
  <n-tab-pane name="MAA-公招设置"><maa-recruit /></n-tab-pane>
  <n-tab-pane name="MAA-刷理智"><MaaWeekly /></n-tab-pane>
</n-tabs>
</div>
</template>

<style scoped lang="scss">
.coord {
  td {
    width: 120px;

    &:nth-child(1),
    &:nth-child(3) {
      width: 30px;
    }

    &:nth-child(2) {
      padding-right: 30px;
    }
  }
}
</style>
