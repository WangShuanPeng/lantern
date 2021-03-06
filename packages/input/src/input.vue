<template>
  <div
    class="lt-input-wrap"
    :class="{maxlength: this.maxlength}"
  >
    <input
      v-if="this.type !== 'textarea'"
      class="lt-input"
      :class="{disabled: this.disabled}"
      ref="input"
      :type="type"
      :disabled="this.disabled"
      :readonly="this.readonly"
      :autofocus="autofocus"
      :maxlength="this.maxlength"
      :placeholder="this.placeholder"
      v-model="inputValue"
      @keyup.enter="handleEnter"
      @blur="handleBlur"
      @change="$emit('change', $event.target.value)"
      @input="handleInput"
    >
    <textarea
      v-else
      class="lt-textarea"
      :class="{disabled: this.disabled, drag: this.drag}"
      ref="textarea"
      :disabled="this.disabled"
      :readonly="this.readonly"
      :autofocus="autofocus"
      :maxlength="this.maxlength"
      :placeholder="this.placeholder"
      :rows="this.rows"
      v-model="inputValue"
      @keyup.enter="handleEnter"
      @change="$emit('change', $event.target.value)"
    />
    <span
      v-if="this.maxlength && this.maxlengthShow"
      class="maxlengthtips"
    >
      {{ this.maxlengthtips }}
    </span>
  </div>
</template>
<script>
import { oneOf, findComponentUpward } from '../../../src/utils/assist'
import Emitter from '../../../src/mixins/emitter'

export default {
  name: 'Input',
  mixins: [ Emitter ],
  data () {
    return {
      inputValue: this.value
    }
  },
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    // input类型
    type: {
      validator (value) {
        return oneOf(value, ['text', 'textarea', 'password', 'url', 'email', 'date'])
      },
      default: 'text'
    },
    placeholder: {
      type: String,
      default: '请输入'
    },
    maxlength: {
      type: [Number, String],
      default: ''
    },
    disabled: {
      type: Boolean,
      default: false
    },
    drag: {
      type: Boolean,
      default: false
    },
    readonly: {
      type: Boolean,
      default: false
    },
    autofocus: {
      type: Boolean,
      default: false
    },
    number: {
      type: Boolean,
      default: false
    },
    rows: {
      type: [Number, String],
      default: 4
    },
    maxlengthShow: {
      type: Boolean,
      default: true
    }
  },
  computed: {
    maxlengthtips () {
      return (this.inputValue ? this.inputValue.length : '0') + '/' + this.maxlength
    }
  },
  watch: {
    value (v) {
      this.inputValue = v
    }
  },
  methods: {
    handleEnter (event) {
      this.$emit('on-enter', event)
    },
    handleBlur (event) {
      this.$emit('on-blur', event)
      if (!findComponentUpward(this, ['DatePicker', 'TimePicker', 'Cascader', 'Search'])) {
        this.dispatch('FormItem', 'on-form-blur', this.currentValue)
      }
    },
    focus() {
      if (this.type === 'textarea') {
        this.$refs.textarea.focus()
      } else {
        this.$refs.input.focus()
      }
    },
    handleInput (event) {
      let value = event.target.value
      if (this.number && value !== '') value = Number.isNaN(Number(value)) ? value : Number(value)
      this.$emit('input', value)
      // this.setCurrentValue(value)
      this.$emit('change', value)
    }
  }
}
</script>
