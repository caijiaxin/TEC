<template>
  <v-container>
    <h1>{{ $t('message') }}</h1>
    <!-- <v-btn @click="changeLocal">切换语言</v-btn> -->
  </v-container>
</template>

<script>
  export default {
    name: 'HelloWorld',
    data() {
      return {
        msg: 'hello'
      }
    },
    methods: {
      changeLocal() {
        this.$i18n.locale = this.$i18n.locale === 'en' ? 'ja' : 'en'
      }
    },
  }
</script>
