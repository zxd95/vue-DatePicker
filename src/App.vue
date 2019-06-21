<template>
  <div id="app">
    <date-picker v-model="value"></date-picker>
  </div>
</template>

<script>
import DatePicker from '@/components/DatePicker/date-picker'

export default {
  name: 'App',
  components: {
    DatePicker
  },
  data () {
    return {
      value: new Date()
    }
  }
}
</script>

<style lang="scss">
</style>
