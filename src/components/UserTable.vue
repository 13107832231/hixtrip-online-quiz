<script setup lang="ts">
  import { onMounted, reactive, ref, watch, computed } from 'vue'
  import userApi from '../api/user'

  let user = ref<any>([])
  let serchValue = ref<string>('')

  const props = defineProps({
    activeMenu: {
      type: Object
    },
    activeSubMenu: {
      type: Object
    }
  })

  // 获取用户数据
  const getUserData = (orgId: string) => {
    userApi.query({ orgId }).then((res) => (user.value = res))
    serchValue.value = ''
  }
  // 关键字搜索
  const getUserResult = computed(() => {
    return user.value.filter((item) => {
      return item.name.indexOf(serchValue.value) !== -1
    })
  })
  watch(
    () => props.activeMenu.id,
    (newValue, oldValue) => {
      if (newValue) {
        serchValue.value = ''
        getUserData(newValue)
      } else {
        user.value = []
      }
    }
  )
  watch(
    () => props.activeSubMenu.id,
    (newValue, oldValue) => {
      if (newValue) {
        getUserData(newValue)
      }
    }
  )
</script>

<template>
  <div class="ctn">
    <span>{{ props.activeMenu.name }}</span>
    <span v-if="props.activeSubMenu.name">/{{ props.activeSubMenu.name }}</span>
    <div class="input-box">
      <input type="text" placeholder="请输入关键字查询" v-model="serchValue" />
    </div>
    <table v-if="getUserResult && getUserResult.length" class="table" border>
      <tr>
        <th>编号</th>
        <th>姓名</th>
      </tr>

      <tr v-for="item in getUserResult" :key="item.id">
        <td>{{ item.id }}</td>
        <td>{{ item.name }}</td>
      </tr>
    </table>
    <div v-else class="data-null">暂无数据</div>
  </div>
</template>

<style scoped>
  .ctn {
    flex: 1;
    padding-left: 30px;
  }
  .table {
    width: 100%;
  }
  .data-null {
    width: 100%;
    height: 200px;
    text-align: center;
    color: #000;
    line-height: 200px;
  }
  .input-box {
    padding: 10px 0px;
  }
</style>
