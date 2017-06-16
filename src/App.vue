<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab ">
      <div class="tab-item">
        <router-link :to="{path:'/goods'}">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{path:'/ratings'}">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{path:'/seller'}">商家</router-link>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
  import VHeader from './components/header/header.vue'
  const ERRNo = 0
  export default {
    data () {
      return {
        seller: {}
      }
    },
    created () {
      this.$http.get('/api/seller').then((res) => {
        if (res.data.errno === ERRNo) {
          this.seller = res.data.data
          console.log(this.seller)
        }
      })
    },
    components: {
      VHeader
    },
    name: 'app'
  }
</script>

<style lang="less">
  @import url(./common/stylus/mixin.less);

  .tab {
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    .tab-item {
      flex: 1;
      text-align: center;
    }
    .border-1px(red)

  }

  .active {
    color: rgb(240, 20, 20);
  }
</style>
