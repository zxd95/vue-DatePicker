<template>
  <div v-click-outside>
    <input
      type="text"
      class="input"
      :value="formatDate"
      :placeholder="placeholderText"
      @focus="handleFocus"
      @blur="handleBlur"
    >
    <div v-if="isVisible" class="pannel">
      <div>d</div>
    </div>
  </div>
</template>

<script>
import * as utlis from './util.js'

export default {
  name: 'date-paicker',
  props: {
    value: {
      type: Date,
      default: () => new Date()
    }
  },
  directives: {
    clickOutSide: {
      bind () {
        
      }
    }
  },
  data () {
    return {
      placeholderText: '请选择日期',
      isVisible: false // 日期面板显隐
    }
  },
  computed: {
    formatDate () {
      let { year, month, day } = utlis.getFormatDate(this.value)
      return `${year}-${month}-${day}`
    }
  },
  methods: {
    handleFocus () {
      this.isVisible = true
    },
    handleBlur () {
      this.isVisible = false
    }
  }
}
</script>

<style lang="scss" scoped>
  .input {
    display: inline-block;
    width: 180px;
    height: 30px;
    line-height: 30px;
    padding: 0 8px;
    color: #333;
    font-size: 12px;
    border: 1px solid #ddd;
    border-radius: 2px;
    cursor: text;
    transition: border .2s ease-in-out, background .2s ease-in-out, box-shadow .2s ease-in-out;
      &:hover {
        border: 1px solid #1890ff;
      }
      &:focus {
        border-color: #1890ff;
        outline: none;
        box-shadow: none;
      }
  }
  .pannel {

  }
</style>
