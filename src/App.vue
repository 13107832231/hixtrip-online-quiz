<script setup lang="ts">
  import { onMounted, reactive, ref, computed } from 'vue'
  import orgApi from './api/org'
  import UserTree from './components/UserTable.vue'

  let org = ref<any>()

  let activeMenu = reactive<any>({})
  let activeSubMenu = reactive<any>({})

  onMounted(() => {
    orgApi.query().then((res) => (org.value = res))
  })

  // 获取子菜单数据
  const getChildData = (item, id: string) => {
    item.isOpen = Boolean(!item.isOpen)
    activeSubMenu.id = ''
    activeSubMenu.name = ''
    // 收起状态清空子菜单数据
    if (!item.isOpen) {
      item.children = []
      return
    }

    activeMenu.id = item.id
    activeMenu.name = item.name

    orgApi.query({ parentId: id }).then((res) => {
      item.children = res
    })
  }
  // 点击子菜单
  const clickSubMenu = (item) => {
    activeSubMenu.id = item.id
    activeSubMenu.name = item.name
  }
</script>

<template>
  <div class="main">
    <ul class="menu-box">
      <div v-for="item in org" :key="item.id">
        <li
          class="menu-item"
          :class="{ active: activeMenu.id === item.id }"
          @click="getChildData(item, item.id)"
        >
          <span>{{ item.name }}</span>
          <span
            class="icon"
            :class="{
              isOpen: item.isOpen
            }"
            >></span
          >
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
    <UserTree :activeMenu="activeMenu" :activeSubMenu="activeSubMenu" />
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
    display: flex;
    text-align: left;
    height: 100%;
  }
  .menu-box {
    width: 200px;
    border: 1px solid #eee;
    height: 100%;
    overflow-y: auto;
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

  .menu-item .icon.isOpen {
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
</style>
