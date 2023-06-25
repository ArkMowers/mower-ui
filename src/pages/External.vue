<script setup>
import { useConfigStore } from '@/stores/config'
import { storeToRefs } from 'pinia'
import { inject, h } from 'vue'
import { NTag } from 'naive-ui'

const axios = inject('axios')

const store = useConfigStore()

const {
  mail_enable,
  account,
  pass_code,
  maa_enable,
  maa_path,
  maa_adb_path,
  maa_weekly_plan,
  maa_rg_enable
} = storeToRefs(store)

async function select_maa_dir() {
  const response = await axios.get(`${import.meta.env.VITE_HTTP_URL}/dialog/folder`)
  const folder_path = response.data
  if (folder_path) {
    maa_path.value = folder_path
  }
}

async function select_maa_adb_path() {
  const response = await axios.get(`${import.meta.env.VITE_HTTP_URL}/dialog/file`)
  const file_path = response.data
  if (file_path) {
    maa_adb_path.value = file_path
  }
}

function render_tag({ option, handleClose }) {
  return h(
    NTag,
    {
      type: option.type,
      closable: true,
      onMousedown: (e) => {
        e.preventDefault()
      },
      onClose: (e) => {
        e.stopPropagation()
        handleClose()
      }
    },
    {
      default: () => {
        if (option.label == '') {
          return '上次作战'
        } else if (option.label == 'Annihilation') {
          return '当期剿灭'
        } else if (option.label.endsWith('-HARD')) {
          return option.label.slice(0, -5) + '磨难'
        } else if (option.label.endsWith('-NORMAL')) {
          return option.label.slice(0, -7) + '标准'
        } else {
          return option.label
        }
      }
    }
  )
}

function create_tag(label) {
  if (label == ' ') {
    return {
      label: '上次作战',
      value: ''
    }
  } else if (label == '当期剿灭') {
    return {
      label: '当期剿灭',
      value: 'Annihilation'
    }
  } else if (label.endsWith('磨难')) {
    return {
      label: label,
      value: label.slice(0, -2) + '-HARD'
    }
  } else if (label.endsWith('标准')) {
    return {
      label: label,
      value: label.slice(0, -2) + '-NORMAL'
    }
  } else {
    return {
      label,
      value: label
    }
  }
}
</script>

<template>
  <div class="home-container">
    <n-card>
      <template #header>
        <n-checkbox v-model:checked="mail_enable">
          <div class="card-title">邮件提醒</div>
        </n-checkbox>
      </template>
      <template #default>
        <table>
          <tr>
            <td class="table-space">QQ邮箱</td>
            <td class="table-space"><n-input v-model:value="account"></n-input></td>
            <td class="table-space">授权码</td>
            <td>
              <n-input v-model:value="pass_code" type="password" show-password-on="click"></n-input>
            </td>
          </tr>
        </table>
      </template>
    </n-card>
    <n-card>
      <template #header>
        <n-checkbox v-model:checked="maa_enable">
          <div class="card-title">MAA</div>
        </n-checkbox>
      </template>
      <template #default>
        <table class="maa-table">
          <tr>
            <td class="table-space maa-table-label">MAA目录</td>
            <td class="input-td"><n-input v-model:value="maa_path"></n-input></td>
            <td class="table-space">
              <n-button @click="select_maa_dir">...</n-button>
            </td>
          </tr>
          <tr>
            <td class="table-space">adb地址</td>
            <td><n-input v-model:value="maa_adb_path"></n-input></td>
            <td>
              <n-button @click="select_maa_adb_path">...</n-button>
            </td>
          </tr>
          <tr>
            <td class="table-space">肉鸽：</td>
            <td colspan="3">
              <n-radio-group v-model:value="maa_rg_enable">
                <n-space>
                  <n-radio :value="1">启用</n-radio>
                  <n-radio :value="0">禁用</n-radio>
                </n-space>
              </n-radio-group>
            </td>
          </tr>
        </table>
        <n-h3>周计划</n-h3>
        <span>关卡填写说明：</span>
        <ul>
          <li><b>添加关卡</b>：输入关卡名，按回车键确认。文本变为标签，代表输入成功。</li>
          <li><b>上次作战</b>：输入空格后回车，生成 <n-tag closable>上次作战</n-tag> 标签。</li>
          <li><b>主线关卡难度</b>：在关卡末尾添加“标准”或“磨难”以指定难度。例：</li>
          <ul>
            <li><n-tag closable>12-17标准</n-tag> 表示12-17标准难度。</li>
            <li><n-tag closable>12-17磨难</n-tag> 表示12-17磨难难度。</li>
          </ul>
          <li>
            <b>当期剿灭</b>：输入“当期剿灭”后回车，生成 <n-tag closable>当期剿灭</n-tag> 标签。
          </li>
          <li>
            <b>信用作战</b>：若当日计划不包含 <n-tag closable>上次作战</n-tag>，则自动进行信用作战。
          </li>
          <li>
            <b>多个关卡</b
            >：填入多个关卡时，按顺序依次刷取所有关卡。关卡无法刷取或刷取结束后，继续尝试下一关卡。例：
            <ul>
              <li>
                <n-tag closable class="tag-mr">HE-7</n-tag>
                <n-tag closable>上次作战</n-tag>
                ：刷活动关HE-7，若活动未开放，则刷上一关。
              </li>
              <li>
                <n-tag closable class="tag-mr">AP-5</n-tag>
                <n-tag closable>1-7</n-tag>
                ：刷红票本AP-5，剩余体力刷1-7。
              </li>
            </ul>
          </li>
          <li><b>不刷理智</b>：留空表示不刷理智。</li>
        </ul>
        <table>
          <tr v-for="plan in maa_weekly_plan" :key="plan.weekday">
            <td class="table-space">
              <n-h4>{{ plan.weekday }}</n-h4>
            </td>
            <td>关卡：</td>
            <td class="table-space maa-stage">
              <n-select
                v-model:value="plan.stage"
                multiple
                filterable
                tag
                :show="false"
                :show-arrow="false"
                :render-tag="render_tag"
                :on-create="create_tag"
              />
            </td>
            <td>理智药：</td>
            <td>
              <n-input-number v-model:value="plan.medicine" :min="0"></n-input-number>
            </td>
          </tr>
        </table>
      </template>
    </n-card>
  </div>
</template>

<style scoped>
h4 {
  margin: 0;
}

.card-title {
  font-weight: 500;
  font-size: 16px;
}

.maa-table {
  width: 100%;
}

.maa-table-label {
  width: 70px;
}

.maa-stage {
  min-width: 300px;
}

ul {
  padding-left: 24px;
}

.tag-mr {
  margin-right: 4px;
}
</style>
