<template>
  <div>
    <div>
      <div
        v-for="(ex, index) in audios"
        :ref="`audio${index}`"
        :class="{
          disabled: !ex.answered,
          success: (ex.answered && checkAnswer(ex.answers)),
          failed: (ex.answered && !checkAnswer(ex.answers))
        }"
        @click="viewCurrent(ex, index)"
      >
        <AudioPlayer :audio="ex"/>
      </div>
    </div>
    <div>
      <div v-for="(aswr, index) in current.answers" @click="selectAnswer(aswr, $event.target)" :ref="`answer${index}`" :class="{ right: checkRight(aswr, current), wrong: checkWrong(aswr, current) }">
        {{ aswr.text }}
      </div>
    </div>
    <div>
      <button @click="setCurrent(audios)" v-if="current.answered && !ended">Next</button>
      <p v-if="ended">You've finished!</p>
    </div>
  </div>
</template>
<script>
import AudioPlayer from '../../components/AudioPlayer'

export default {
  name: "Answers",
  data () {
    return {
      audios: [],
      current: {
        answers: [],
        answered: false
      },
      ended: false,
      currentIndex: null
    }
  },
  methods: {
    setCurrent (audios) {
      for (const index in audios) {
        if (!audios[index].answered) {
          setTimeout(() => {
            this.$refs[`audio${index}`][0].classList.remove('disabled')
          }, 0)
          this.checkCurrent(index)
          break
        } else {
          if (parseInt(index) === (audios.length - 1)) {
            this.ended = 'true'
            this.current = audios[index]
          }
        }
      }
    },
    selectAnswer (answer, el) {
      if (!this.current.answered) {
        answer.selected = true
        const elAudio = this.$refs[`audio${this.currentIndex}`][0]
        if (answer.correct) {
          el.classList.add('right')
          elAudio.classList.add('success')
        } else {
          el.classList.add('wrong')
          elAudio.classList.add('failed')
          const correctIndex = this.current.answers.findIndex(x => x.correct)
          this.$refs[`answer${correctIndex}`][0].classList.add('right')
        }
        this.current.answered = true
      }
    },
    resetBackground () {
      const right = document.querySelector('.right')
      const wrong = document.querySelector('.wrong')
      if (right) right.classList.remove('right')
      if (wrong) wrong.classList.remove('wrong')
    },
    setGame (audios) {
      this.ended = false
      this.audios = audios
      this.setCurrent(this.audios)
    },
    checkRight (answer, audio) {
      if (answer.correct && audio.answered) return true
      else return false
    },
    checkWrong (answer, audio) {
      if (answer.selected && !answer.correct && audio.answered) return true
      else return false
    },
    checkAnswer (answers) {
      const selectedIndex = answers.findIndex(x => x.selected)
      if (selectedIndex >= 0) {
        if (answers[selectedIndex].correct) return true
      }
      return false
    },
    checkCurrent (index) {
      this.currentIndex = index
      this.resetBackground()
      this.current = this.audios[index]
    },
    viewCurrent (audio, index) {
      if (audio.answered) this.checkCurrent(index)
    }
  },
  components: {
    AudioPlayer
  }
}
</script>
<style lang="scss" scoped>
.right {
  background-color: green;
}

.wrong {
  background-color: red;
}

.disabled {
  background-color: gray
}

.failed {
  background-color: red
}

.success {
  background-color: green
}
</style>