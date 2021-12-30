<template>
  <div class="simon__option">
    <input
      type="radio"
      name="mode"
      :id="id"
      :value="value"
      @change="updateInputValue"
      :checked="checked === value"
    >
    <label :for="id">{{ label }}</label>
  </div>
</template>
<script>
export default {
  emits: {
    'change-level'(str) {
      if (str) {
        return true
      }
      console.warn('No data in change-level emit')
      return false
    }
  },
  props: {
    id: {
      type: String,
      required: true,
    },
    value: {
      type: String,
      required: true
    },
    label: {
      type: String,
      required: true
    },
    checked: {
      type: String,
      required: true
    }
  },
  methods: {
    updateInputValue(e) {
      this.$emit('change-level', e.target.value)
    }
  }
}
</script>
<style></style>
