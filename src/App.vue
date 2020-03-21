<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
    <v-btn @click="reset" v-if="showResult" color="green">再算一次</v-btn>
    <v-spacer/>
    {{ title }}
    <v-spacer/>
    <span>日语</span>
    </v-app-bar>
    <v-content ref="content">
      <result-page v-if="showResult" :results="results"/>
      <calc-form v-else @calc="calc"/>
    </v-content>
  </v-app>
</template>

<script>
import CalcForm from './components/CalcForm'
import ResultPage from './components/ResultPage'

export default {
  name: 'App',
  components: {
    CalcForm,
    ResultPage
  },
  data() {
    return {
      title: '手机话费计算器',
      showResult: false,
      results: {
        planName: '',
        dataCost: '',
        phoneCost: '',
        currentCost: '',
        isNoneCall: true
      }
    }
  },
  methods: {
    /**
     * 主要计算逻辑
     * 控制是否显示某些要素，以及设置计算后的值
     */
    calc(params) {
      this.results.planName = this.calcPlanName(params.dataPlan.value)
      this.results.dataCost = params.dataPlan.price
      this.results.phoneCost = this.calcPhoneCost(params.callTime)
      this.results.currentCost = params.phoneCost + params.poketWifiCost
      this.results.isNoneCall = params.isNoneCall
      this.showResult = true
      // scroll to top
      this.$refs.content.scrollTop = 0
    },
    calcPlanName(data) {
      if (data > 20) {
        return '可接收短信的100M流量卡 + 移动wifi'
      }
      return data + 'G流量卡'
    },
    calcPhoneCost(time) {
      return (time * 60 / 30 * 8)
    },
    reset() {
      this.showResult = false
    }
  }
}
</script>
