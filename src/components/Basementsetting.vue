<script setup>
import { useConfigStore } from '@/stores/config'
import { usePlanStore } from '@/stores/plan'
import { storeToRefs } from 'pinia'
import { computed } from 'vue'

const config_store = useConfigStore()
const plan_store = usePlanStore()

const {

  run_order_delay,
  drone_room,
  drone_count_limit,
  reload_room,

  free_blacklist,

  resting_threshold,

} = storeToRefs(config_store)

const { operators } = storeToRefs(plan_store)

const { left_side_facility } = plan_store

const facility_with_empty = computed(() => {
  return [{ label: '（加速贸易站）', value: '' }].concat(left_side_facility)
})


</script>

<template>
  <n-card title="基建设置">
    <table class="riic-conf">
      <tr>
        <td>宿舍黑名单：</td>
        <td colspan="2">
          <n-select multiple filterable tag :options="operators" v-model:value="free_blacklist" />
        </td>
      </tr>
      <tr>
        <td>跑单前置延时：</td>
        <td>
          <n-input-number v-model:value="run_order_delay" />
        </td>
        <td>分钟（可填小数）</td>
      </tr>
      <tr>
        <td>无人机使用房间：</td>
        <td colspan="2">
          <n-select :options="facility_with_empty" v-model:value="drone_room" />
        </td>
      </tr>
      <tr>
        <td>无人机使用阈值：</td>
        <td colspan="2">
          <n-input-number v-model:value="drone_count_limit" />
        </td>
      </tr>
      <tr>
        <td>搓玉补货房间：</td>
        <td colspan="2">
          <n-select multiple filterable tag :options="left_side_facility" v-model:value="reload_room" />
        </td>
      </tr>
      <tr>
        <td>心情阈值：</td>
        <td colspan="2">
          <div class="threshold">
            <n-slider v-model:value="resting_threshold" :step="0.05" :min="0.5" :max="0.8" />
            <n-input-number v-model:value="resting_threshold" />
          </div>
        </td>
      </tr>
    </table>
  </n-card>
</template>

<style scoped lang="scss">
.threshold {
  display: flex;
  align-items: center;
  gap: 14px;
}



.riic-conf {
  width: 100%;

  td {
    &:nth-child(1) {
      width: 120px;
    }

    &:nth-child(3) {
      padding-left: 12px;
      width: 120px;
    }
  }
}
</style>
