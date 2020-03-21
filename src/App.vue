<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
    <v-btn @click="reset" v-if="showResult" color="green">{{ $t('resetBtn') }}</v-btn>
    <v-spacer/>
    {{ $t('title') }}
    <v-spacer/>
    <v-btn @click="changeLocal" color="pink"><v-icon left>mdi-earth</v-icon>{{ $t('changeLocal') }}</v-btn>
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
    },
    calcPlanName(data) {
      if (data > 20) {
        return this.$t('leastPlan')
      }
      return data + 'G' + this.$t('dataSIM')
    },
    calcPhoneCost(time) {
      return (time * 60 / 30 * 8)
    },
    reset() {
      this.showResult = false
    },
    changeLocal() {
      this.$i18n.locale = this.$i18n.locale === 'en' ? 'ja' : 'en'
    }
  }
}
</script>
