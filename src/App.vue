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
    <v-content>
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
      this.results.planName = this.calcPlanName(params.dataPlan)
      this.results.dataCost = this.calcDataCost(params.dataPlan)
      this.results.phoneCost = this.calcPhoneCost(params.callTime)
      this.results.currentCost = params.phoneCost
      this.results.isNoneCall = params.isNoneCall
      this.showResult = true
    },
    calcPlanName(data) {
      if (data === 10) {
        return '3G流量卡 + 无限移动wifi'
      }
      return data + 'G流量卡'
    },
    calcPhoneCost(time) {
      return (time * 60 / 30 * 8)
    },
    calcDataCost(data) {
      switch (data) {
        case 3:
          return 1100
        case 5:
          return 1100 + (2 * 1100)
        case 7:
          return 1100 + (4 * 1100)
        default:
          return 3880 * 1.1 + 1100
      }
    },
    reset() {
      this.showResult = false
    }
  }
}
</script>
