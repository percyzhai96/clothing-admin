<template>
  <div class="sidebar">
    <div style="text-align: left" class="sidebar-title side_text_style">
      {{ title }}
    </div>
    <div class="sidebar-box">
      <el-menu
        class="el-menu-vertical-demo"
        :collapse="!showSidebar"
        :class="showSidebar ? 'sidebar-menu' : ''"
        :default-active="activeMenu"
        background-color="transparent"
        :text-color="variables.menuText"
        :unique-opened="true"
        :active-text-color="variables.menuActiveText"
        :collapse-transition="true"
        mode="vertical"
      >
        <SidebarItem
          v-for="(route, index) in routes"
          :key="index"
          :item="route"
          :base-path="route.path"
        />
      </el-menu>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import SidebarItem from './SidebarItem.vue'

export default {
  components: { SidebarItem },
  data() {
    return {
      title: '🌸RUI-ADMIN',
    }
  },
  computed: {
    ...mapState('theme', ['showSidebar']),
    ...mapState('permission', ['routes']),
    activeMenu() {
      const route = this.$route
      const { meta, path } = route
      // 默认激活项
      if (meta.activeMenu) {
        return meta.activeMenu
      }
      return path
    },
    variables() {
      return {
        menuText: '#ffffff', // "#bfcbd9"
        menuActiveText: '#ffffff',
      }
    },
  },
  mounted() {
    if (!this.showSidebar) {
      this.title = ''
    }
  },
  watch: {
    showSidebar(v) {
      if (v) {
        setTimeout(() => {
          this.title = 'Autchan'
        }, 300)
      } else {
        this.title = ''
      }
    },
  },
}
</script>

<style scoped>
div.side_text_style {
  box-sizing: border-box;
  padding-left: 20px;
}
</style>
