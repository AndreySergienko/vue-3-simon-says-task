<template>
  <div class="alert" :class="type" @click="closeAlert">
    Вы проиграли, счёт: {{ round }}
    Сложность: {{ difficultyRus }}
  </div>
</template>

<script>
export default {
  emits: {
    'close': null
  },
  props: {
    type: {
      type: String,
      required: false,
      default: 'lose'
    },
    difficulty: {
      type: String,
      required: false,
      default: 'easy'
    },
    round: {
      type: Number,
      required: true
    }
  },
  methods: {
    closeAlert() {
      this.$emit('close')
    }
  },
  computed: {
    difficultyRus() {
      switch (this.difficulty) {
        case 'easy':
          return 'Легко'
        case 'normal':
          return 'Нормально'
        case 'hard':
          return 'Сложно'
      }
    }
  }
}
</script>
