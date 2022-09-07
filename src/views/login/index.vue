<template>
  <div>
    <!-- 头部导航 -->
    <van-nav-bar title="登录" class="nav-bar" />
    <!-- 表单 -->
    <van-form @submit="onSubmit" class="form" ref="form">
      <van-field
        v-model="moblie"
        name="moblie"
        placeholder="请输入手机号"
        :rules="mobileRules"
      >
        <template #label>
          <span class="toutiao toutiao-shouji"></span>
        </template>
      </van-field>
      <van-field
        v-model="code"
        name="code"
        placeholder="请输入验证码"
        :rules="codeRules"
      >
        <template #label>
          <span class="toutiao toutiao-yanzhengma"></span>
        </template>
        <template #button>
          <van-button
            class="btn"
            block
            type="default"
            size="small"
            round
            native-type="button"
            v-if="isShowCodeBtn"
            @click="sendCode"
          >
            获取验证码
          </van-button>
          <van-count-down
            v-else
            :time="60 * 1000"
            format="ss秒"
            @finish="isShowCodeBtn = true"
          />
        </template>
      </van-field>
      <div style="margin: 16px">
        <van-button block type="info" native-type="submit">登录</van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
import { mobileRules, codeRules } from './rule'

import { login, sendCodeAPI } from '@/api'
import { mapMutations } from 'vuex'
export default {
  data() {
    return {
      moblie: '',
      code: '',
      mobileRules,
      codeRules,
      isShowCodeBtn: true
    }
  },
  methods: {
    ...mapMutations(['SET_TOKEN']),
    async onSubmit() {
      // submit事件只有表单校验通过后会被触发

      // 登录
      // const { data } = await login(this.moblie, this.code)
      // console.log(data)
      this.$toast.loading({
        message: '国伟冲啊...',
        forbidClick: true,
        duration: 0
      })

      try {
        // 登录
        const { data } = await login(this.moblie, this.code)
        this.SET_TOKEN(data.data)
        // console.log(data)

        // 跳转路由
        this.$router.push('/profile')
        // 成功提示
        this.$toast.success('冲刺成功')
      } catch (error) {
        // this.$toast.fail('冲刺失败')
        // 细分失败：提示用户手机号和验证码
        // 如果是手机号或者验证码错了，用户能知道
        // error：1.js抛的错 2. axios封装的error对象

        // axios封装的error脆响
        // -error.response.data 后端返回的数据
        // -error.response.status 后端返回的状态码
        if (error.response && error.response.status === 400) {
          this.$toast.fail(error.response.data.message)
        } else {
          // 1. js导致的错误， 2. 507
          console.dir(error)
          this.$toast.clear()
        }
      }
    },
    loading() {
      this.$toast.loading({
        message: '加载中...',
        forbidClick: true,
        duration: 0
      })
    },
    async sendCode() {
      await this.$refs.form.validate('moblie')

      this.loading()
      // 1.发送请求
      // console.log('发送国伟')
      try {
        await sendCodeAPI(this.moblie)
        // 2.显示倒计时组件
        this.isShowCodeBtn = false

        this.$toast.success('发送验证码成功')
      } catch (error) {
        if (
          error.response &&
          (error.response.status === 429 || error.response.status === 404)
        ) {
          // axios 的错误
          this.$toast.fail(error.response.data.message)
        } else {
          this.$toast.clear()
          throw error
        }
      }
    }
  }
}
</script>

<style lang="less" scoped>
// scoped:样式作用于当前的组件
// vue-cli 提供了语法 :deep() 深度选择器
.nav-bar {
  background-color: #3296fa;
  :deep(.van-nav-bar__title) {
    color: #fff;
  }
}
:deep(.form) {
  .van-cell__title {
    flex: 1;
  }
  .van-cell__value {
    flex: 20;
  }
  .toutiao {
    font-size: 40px;
  }
}

.btn {
  height: 0.64rem;
  background-color: #eee;
  color: #a58594;
}
</style>
