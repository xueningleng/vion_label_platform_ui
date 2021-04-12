<template>
  <div :class="classObj" class="app-wrapper">
    <div class = "fixed-header">
    <h1>
      标注云平台</h1></div>
    <div class = "fixed-header2"></div>

    <div v-if="device==='mobile'&&sidebar.opened" class="drawer-bg" @click="handleClickOutside" />
    <sidebar class="sidebar-container" />
    <div class="main-container">
      <div :class="{'header-2':fixedHeader}">
        <navbar />
      </div>
      <app-main />
    </div>
  </div>
</template>

<script>
import { Navbar, Sidebar, AppMain } from './components'
import ResizeMixin from './mixin/ResizeHandler'

export default {
  name: 'Layout',
  components: {
    Navbar,
    Sidebar,
    AppMain
  },
  mixins: [ResizeMixin],
  computed: {
    sidebar() {
      return this.$store.state.app.sidebar
    },
    header()  {
      return this.$store.state.app.header
    },
    device() {
      return this.$store.state.app.device
    },
    fixedHeader() {
      return this.$store.state.settings.fixedHeader
    },
    classObj() {
      return {
        hideSidebar: !this.sidebar.opened,
        openSidebar: this.sidebar.opened,
        withoutAnimation: this.sidebar.withoutAnimation,
        mobile: this.device === 'mobile'
      }
    }
  },
  methods: {
    handleClickOutside() {
      this.$store.dispatch('app/closeSideBar', { withoutAnimation: false })
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "~@/styles/mixin.scss";
  @import "~@/styles/variables.scss";


  .app-wrapper {
    @include clearfix;
    position: relative;
    height: 100%;
    width: 100%;
    &.mobile.openSidebar{
      position: fixed;
      top: 0;
    }
  }
  .drawer-bg {
    background: #000;
    opacity: 0.3;
    width: 100%;
    top: 0;
    height: 100%;
    position: absolute;
    z-index: 999;
  }

  .fixed-header{
    background: $major-color5;
    position: fixed;
    top: 0;
    right: 0;
    z-index:99;
    width: 100%;
    height: 100px;
    h1{
        font-family:微软雅黑;
        font-size: 32px;
        font-weight:bold;
        color: $major_color7;
        position: fixed;
        left: 20px;
        top: 10px;
        z-index: 90;            
    }
  }
  
  .fixed-header2{
    background: $major-color6;
    position: fixed;
    top: $fixedHeadingHeight;
    right: 0;
    z-index:98;
    width: 100%;
    height: 100px;
    
  }
  .header-2 {
    background-color: #{$major-color3};
    position: fixed;
    top: 100px;
    right: 0;
    z-index: 9;
    width: calc(100% - #{$sideBarWidth});
    transition: width 0.28s;
  }

  .hideSidebar .header-2 {
    width: calc(100% - 54px)
  }

  .mobile .header-2 {
    width: 100%;
  }
</style>
