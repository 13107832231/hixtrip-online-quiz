<script setup lang="ts">
  import { onMounted, reactive, ref, computed } from 'vue'
  import userApi from './api/user'
  import orgApi from './api/org'

  let user = ref<any>([])
  let org = ref<any>()

  let activeMenu = reactive<any>({})
  let activeSubMenu = reactive<any>({})
  let serchValue = ref<string>('')
  onMounted(() => {
    // userApi.query({}).then((res) => (user.value = res))
    orgApi.query().then((res) => (org.value = res))
  })
  // 获取用户数据
  const getUserData = (orgId: string) => {
    userApi.query({ orgId }).then((res) => (user.value = res))
  }
  // 获取子菜单数据
  const getChildData = (item, id: string) => {
    activeSubMenu.id = ''
    activeSubMenu.name = ''
    serchValue.value = ''
    //
    if (activeMenu.id === id) {
      activeMenu.id = ''
      activeMenu.name = ''
      user.value = []
      item.children = []
      return
    }
    getUserData(id)
    activeMenu.id = item.id
    activeMenu.name = item.name

    orgApi.query({ parentId: id }).then((res) => {
      item.children = res

      console.log(org.value, 'ddfdf')
      console.log(res, 'dasdas')
    })
  }
  // 点击子菜单
  const clickSubMenu = (item) => {
    getUserData(item.id)
    serchValue.value = ''

    activeSubMenu.id = item.id
    activeSubMenu.name = item.name
  }

  // 关键字搜索
  const getUserResult = computed(() => {
    return user.value.filter((item) => {
      return item.name.indexOf(serchValue.value) !== -1
    })
  })
</script>

<template>
  <div class="main">
    <!-- {{ org }} -->
    <!-- {{ org }}
    {{ user }} -->
    <ul class="menu-box">
      <div v-for="item in org" :key="item.id">
        <li
          class="menu-item"
          :class="{ active: activeMenu.id === item.id }"
          @click="getChildData(item, item.id)"
        >
          <span>{{ item.name }}</span>
          <span class="icon">></span>
        </li>
        <div
          v-if="
            item.children && item.children.length && activeMenu.id === item.id
          "
        >
          <li
            class="menu-item sub-menu"
            :class="{ active: activeSubMenu.id === item.id }"
            v-for="item in item.children"
            :key="item.id"
            @click="clickSubMenu(item)"
          >
            {{ item.name }}
          </li>
        </div>
      </div>
    </ul>
    <div class="ctn">
      <span>{{ activeMenu.name }}</span>
      <span v-if="activeSubMenu.name">/{{ activeSubMenu.name }}</span>
      <div class="input-box">
        <input
          type="text"
          placeholder="请输入关键字查询"
          v-model="serchValue"
        />
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
  </div>
</template>

<style scoped>
  /* .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.vue:hover {
    filter: drop-shadow(0 0 2em #42b883aa);
  } */
  .main {
    width: 100%;
    height: 100vh;
    display: flex;
    text-align: left;
  }
  .menu-box {
    width: 200px;
    border: 1px solid #eee;
  }
  .menu-item {
    padding: 5px 10px;
    cursor: pointer;
    display: flex;
    justify-content: space-between;
  }
  .menu-item:hover,
  .menu-item.active {
    background: lightblue;
  }
  .menu-item.active .icon {
    transform: rotate(90deg);
    transition-duration: 0.3s;
  }
  .menu-item .icon {
    transform: rotate(0deg);
    transition-duration: 0.3s;
  }
  .sub-menu {
    padding-left: 15px;
  }
  .sub-menu.active {
    background: lightgreen;
  }
  .sub-menu:hover {
    background: lightgreen;
  }
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
