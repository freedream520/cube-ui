<template>
  <div class="cube-segment-picker">
    <!-- Direct props will recover the property from v-bind Object expect 'is'. -->
    <!-- To ensure the priority of v-bind Object which is from user configure, we check the property of item in every prop. -->
    <component
      v-for="(item, index) in data"
      :key="index"
      :index="index"
      ref="pickers"
      :is="item.is || 'cube-picker'"
      :title="item.title || title"
      :confirmTxt="item.confirmTxt || (index === data.length - 1 ? confirmTxt : nextTxt)"
      :cancelTxt="item.cancelTxt || (index === 0 ? cancelTxt : prevTxt)"
      v-bind="item"
      @select="_select"
      @cancel="_cancel"
      @change="_change">
    </component>
  </div>
</template>

<script>
const COMPONENT_NAME = 'cube-segment-picker'
const EVENT_NEXT = 'next'
const EVENT_PREV = 'prev'
const EVENT_SELECT = 'select'
const EVENT_CANCEL = 'cancel'
const EVENT_CHANGE = 'change'

export default {
  name: COMPONENT_NAME,
  props: {
    data: {
      type: Array,
      default() {
        return []
      }
    },
    title: {
      type: String,
      default: 'Segment Picker'
    },
    confirmTxt: {
      type: String,
      default: '确定'
    },
    cancelTxt: {
      type: String,
      default: '取消'
    },
    nextTxt: {
      type: String,
      default: '下一步'
    },
    prevTxt: {
      type: String,
      default: '上一步'
    }
  },
  data() {
    return {
      current: 0,
      selectedVal: [],
      selectedIndex: [],
      selectedText: []
    }
  },
  computed: {
    currentPicker () {
      // $refs is not responsive, should not use to computed or watch, so we import this.data to responsive.
      for (let i = 0; i < this.data.length; i++) {
        let item = this.$refs.pickers[i]
        if (item.$attrs.index === this.current) {
          return item
        }
      }
    }
  },
  watch: {
    data() {
      this.current = 0
      this.selectedVal = []
      this.selectedIndex = []
      this.selectedText = []
    }
  },
  methods: {
    show() {
      this.data.length && this.currentPicker.show()
    },
    hide() {
      this.data.length && this.currentPicker.hide()
    },
    _select(...args) {
      this.selectedVal[this.current] = args[0]
      this.selectedIndex[this.current] = args[1]
      this.selectedText[this.current] = args[2]

      if (this.current < this.data.length - 1) {
        this.$emit(EVENT_NEXT, this.current, ...args)
        this.current++
        this.currentPicker.show()
      } else {
        this.$emit(EVENT_SELECT, this.selectedVal, this.selectedIndex, this.selectedText)
        this.current = 0
      }
    },
    _cancel(...args) {
      if (this.current > 0) {
        this.$emit(EVENT_PREV, this.current, ...args)
        this.current--
        this.currentPicker.show()
      } else {
        this.$emit(EVENT_CANCEL)
      }
    },
    _change(...args) {
      this.$emit(EVENT_CHANGE, this.current, ...args)
    }
  }
}
</script>
