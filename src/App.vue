<template>
  <div id="app">
    游戏切换：
    <button @click="getR">rrrr</button>
    <button @click="getO">oooo</button>
    <div id="nav">
      <router-link to="/">Home</router-link>|
      <router-link to="/about">About</router-link>
      <router-link
        v-for="item in asyncRoutes"
        :key="item.name"
        :to='item.path'
      > | {{ item.name}} | </router-link>
    </div>
    <router-view/>
  </div>
</template>
<script>
import createRouter, { router } from '../src/router'
import r from '../src/routes/r'
import o from '../src/routes/o'
const _import = file => () => import(`./components/${file}.vue`)
// const _import = file => require(`./components/${file}.vue`).default

export default {
  data () {
    return {
      asyncRoutes: []
    }
  },
  methods: {
    getR () {
      const rr = JSON.parse(JSON.stringify(r))
      // '../src/components/O1.vue'
      router.matcher = createRouter().matcher
      const as = this.filterAsyncRouter(rr)
      router.addRoutes(as)
      this.asyncRoutes = r
    },
    getO () {
      const oo = JSON.parse(JSON.stringify(o))
      router.matcher = createRouter().matcher
      router.addRoutes(this.filterAsyncRouter(oo))
      this.asyncRoutes = o
    },
    filterAsyncRouter (asyncRouterMap) {
      // 遍历后台传来的路由字符串，转换为组件对象
      const accessedRouters = asyncRouterMap.filter(route => {
        if (route.component) {
          console.log(`./components/${route.component}.vue`)
          // _import(route.component).then(res => {
          //   route.component = res.default
          //   console.log(route)
          // })
          route.component = _import(route.component)
          // console.log(route)
        }
        if (route.children && route.children.length) {
          route.children = this.filterAsyncRouter(route.children)
        }
        return true
      })

      return accessedRouters
    }
  }
}
</script>
<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
