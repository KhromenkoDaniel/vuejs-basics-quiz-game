<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'ScoreBoard',
  props: {
    win_count: {
      type: Number,
      default: 0
    },
    lose_count: {
      type: Number,
      default: 0
    },
    resetScore: {
      type: Function,
      default: () => {}
    }
  }
})
</script>

<template>
  <section class="score">
    Player <span>{{ this.win_count }}</span> x <span>{{ this.lose_count }}</span> Computer
    <button class="reset_score" type="button" @click="this.resetScore()">Reset</button>
  </section>
</template>

<style scoped lang="scss">
section.score {
  position: relative;
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}

button {
  position: absolute;
  right: 24px;
  height: 30px;
  min-width: 60px;
  padding: 0 16px;
  color: #fff;
  background-color: #c01818;
  border: 1px solid #d21440;
  cursor: pointer;
  border-radius: 14px;
}
</style>
