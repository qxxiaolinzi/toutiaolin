<template>
  <div>
    <!-- <router-view></router-view> -->
    <!-- 展示count -->
    <h1>{{ count }}</h1>
    <h1>十倍：{{ bigNum }}</h1>
    <button @click="SET_COUNT(10)">冲啊</button>
  </div>
</template>

<script>
// mapState --> 将 vuex 里面state映射到computed
// 使用
// -引入mapState:函数，返回一个对象
// -参数:['映射的属性']

// mapGetters --> 将vuex里面的getters映射到computed

// mapMutations --> 将vuex里面的mutstiond映射到methods
import { mapGetters, mapMutations, mapState } from 'vuex'
export default {
  methods: {
    // increment() {
    //   this.$store.commit('SET_COUNT', 10)
    // }
    ...mapMutations(['SET_COUNT'])
  },
  computed: {
    ...mapState(['count']),
    ...mapGetters(['bigNum'])
  }
}
</script>

<style scoped>
.login {
  width: 700px;
  height: 80px;
  background: skyblue;
}
</style>
