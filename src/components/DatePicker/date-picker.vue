<template>
  <div v-click-outside class="date-picker">
    <input
      type="text"
      class="input"
      :value="formatDate"
      :placeholder="placeholderText"
    >
    <transition name="fade">
      <div v-if="isVisible" class="pannel">
        <div class="pannel-nav">
          <span>&lt;</span>
          <span @click="prevMonth">&lt;&lt;</span>
          <span>{{ time.year }}年</span>
          <span>{{ time.month + 1 }}月</span>
          <span @click="nextMonth">&gt;&gt;</span>
          <span>&gt;</span>
        </div>
        <div class="pannel-concent">
          <div class="days">
            <span v-for="w in weekDays" :key="w" class="cell">{{ w }}</span>
            <!-- 循环 6 x 7 表格 -->
            <div v-for="i in 6" :key="i">
              <span
                v-for="j in 7"
                :key="j"
                class="cell cell-days"
                :class="[
                  { notCurrentMonth: !isCurrentMonth(visibleDays[(i - 1) * 7 + (j - 1)]) },
                  { today: isToday(visibleDays[(i - 1) * 7 + (j - 1)]) },
                  { select: isSelect(visibleDays[(i - 1) * 7 + (j - 1)]) }
                ]"
                @click="chooseDate(visibleDays[(i - 1) * 7 + (j - 1)])"
              >
                {{ visibleDays[(i - 1) * 7 + (j - 1)].getDate() }}
              </span>
            </div>
          </div>
        </div>
        <div class="pannel-footer">
          今天
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import * as utlis from './util'

export default {
  name: 'date-paicker',
  props: {
    value: {
      type: Date,
      default: () => new Date()
    }
  },
  data () {
    let { year, month } = utlis.getFormatDate(this.value)
    return {
      weekDays: ['日', '一', '二', '三', '四', '五', '六'],
      time: { year, month },
      placeholderText: '请选择日期',
      isVisible: false // 日期面板显隐
    }
  },
  directives: {
    clickOutside: {
      bind (el, binding, vnode) { // vnode 当前context上下文的虚拟节点
        let handler = (e) => {
          // 判断当前目标是否包含在该dom元素内
          if (el.contains(e.target)) {
            // 判断当前面板是否显示
            if (!vnode.context.isVisible) {
              vnode.context.handleFocus()
            }
          } else {
            if (vnode.context.isVisible) {
              vnode.context.handleBlur()
            }
          }
        }
        el.handler = handler
        document.addEventListener('click', handler)
      },
      unbind (el) {
        document.removeEventListener('click', el.handler)
      }
    }
  },
  computed: {
    formatDate () { // 格式化日期 yyyy-mm-dd
      let { year, month, day } = utlis.getFormatDate(utlis.getDate(this.time.year, this.time.month, 1))
      return `${year}-${month + 1}-${day}`
    },
    visibleDays () { // 获取当前是周几
      let { year, month } = utlis.getFormatDate(this.value)
      let currentFirstDay = utlis.getDate(year, month, 1) // yyyy-mm-01
      let week = currentFirstDay.getDay()
      let startDay = currentFirstDay - week * 60 * 60 * 1000 * 24 // 开始日期
      let arr = []

      for (let i = 0; i < 42; i++) { // 循环42天
        arr.push(new Date(startDay + i * 60 * 60 * 1000 * 24))
      }

      return arr
    }
  },
  methods: {
    handleFocus () {
      this.isVisible = true
    },
    handleBlur () {
      this.isVisible = false
    },
    isCurrentMonth (date) {
      let { year, month } = utlis.getFormatDate(utlis.getDate(this.time.year, this.time.month, 1))
      let { year: y, month: m } = utlis.getFormatDate(date)
      return year === y && month === m
    },
    isToday (date) {
      let { year, month, day } = utlis.getFormatDate(new Date())
      let { year: y, month: m, day: d } = utlis.getFormatDate(date)
      return year === y && month === m && day === d
    },
    isSelect (date) {
      let { year, month, day } = utlis.getFormatDate(this.value)
      let { year: y, month: m, day: d } = utlis.getFormatDate(date)
      return year === y && month === m && day === d
    },
    chooseDate (date) {
      this.time = utlis.getFormatDate(date)
      this.$emit('input', date)
      this.handleBlur()
    },
    prevMonth () {
      let d = utlis.Date(this.time.year, this.time.month, 1)
      d.setMonth(d.getMonth() - 1)
      this.time = utlis.getFormatDate(d)
    },
    nextMonth () {
      let d = utlis.Date(this.time.year, this.time.month, 1)
      d.setMonth(d.getMonth() + 1)
      this.time = utlis.getFormatDate(d)
    }
  }
}
</script>

<style lang="scss" scoped>
  .date-picker {
    width: 180px;

    .input {
      display: inline-block;
      width: 100%;
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
      width: 32 * 7px;
      position: absolute;
      background-color: #fff;
      border: 1px solid #ddd;
      border-radius: 2px;

      .pannel-nav {
        display: flex;
        justify-content: space-around;
        height: 30px;

        span {
          cursor: pointer;
          user-select: none;
        }
      }

      .pannel-concent {
        .cell {
          display: inline-flex;
          justify-content: center;
          align-items: center;
          width: 32px;
          height: 32px;
          font-weight: 400;
          box-sizing: border-box;
        }

        .cell-days:hover, .select {
          border: 1px solid #1890ff;
          border-radius: 4px;
        }

        .notCurrentMonth {
          color: gray;
        }

        .today {
          background-color: #1890ff;
          color: #fff;
          border-radius: 4px;
        }
      }

      .pannel-footer {
        height: 30px;
        text-align: center;
      }
    }
  }

  .fade-enter {
    opacity: 0;
  }

  .fade-enter-active {
    transition: opacity 1s;
  }

  .fade-leave-to {
    opacity: 0;
  }

  .fade-leave-active {
    transition: opacity 1s;
  }
</style>
