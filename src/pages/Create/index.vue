<template>
<div>
  <div>
    <div>
      <h2>Create</h2>
    </div>
    <div>
      <Answers :exercise="exercise" ref="answers"/>
    </div>
    <div>
      <div>
        <label for="exercise_title">Title</label>
        <input class="input" id="exercise_title" type="text" v-model="exercise.title">
      </div>
      <div>
        <label for="audio_source">Source</label>
        <input class="input" id="audio_source" type="text" v-model="audio.src" @click="audio.src = 'https://vignette.wikia.nocookie.net/leagueoflegends/images/5/5a/AhriStarGuardian.Attack03.ogg/revision/latest?cb=20171128014755'" />
      </div>
      <div class="margin-bottom-20">
        <label for="audio_image">Image</label>
        <input class="input" id="audio_image" type="text" v-model="audio.img" @click="audio.img = 'https://pa1.narvii.com/6523/f9c9f9becbc37e1cf7d3862e54a1b3e4ef88ecd2_128.gif'" />
      </div>
      <div class="margin-bottom-20">
        <form v-on:submit.prevent="addAnswer(answer)" class="container align-items-end">
          <div class="full-width">
            <label for="audio_answer">Answer</label>
            <input class="input" id="audio_answer" type="text" v-model="answer.text" />
          </div>
          <div>
            <button type="submit" class="btn btn-primary margin-left-10">Add Answer</button>
          </div>
        </form>
      </div>
    </div>

    <div class="flex-basis-300 flex-grow-1 margin-10 item item-border item-form">
      <div v-for="(aswr, index) in audio.answers" class="container align-items-center margin-top-10">
        <input class="margin-right-10" type="radio" name="correct" :checked="aswr.correct" @change="correctChange(aswr)">
        <input type="text" v-model="aswr.text" class="input">
        <button @click="removeAnswer(index)" class="btn btn-danger margin-left-10">Remove</button>
      </div>

      <div class="margin-top-20">
        <button @click="addAudio(audio)" v-if="!isToUpdate" class="btn btn-primary btn-full">Add Audio</button>
        <button @click="update(audio)" v-else class="btn btn-full">Edit</button>
        <button @click="cancelUpdate()" v-if="isToUpdate" class="btn btn-danger btn-full">Calcel</button>
      </div>
    </div>

  </div>

  <div class="margin-top-20 margin-bottom-20 item item-border">
    <div v-for="(ex, index) in exercise.audios" class="item item-border item-form">
      <div>
        <strong>Source:</strong> {{ ex.src }}
      </div>
      <div>
        <strong>Image:</strong> {{ ex.img }}
      </div>
      <div>
        <strong>Answers:</strong> {{ ex.answers.length }}
      </div>
      <div class="margin-top-10">
        <button @click="edit(ex, index)" class="btn">Edit</button>
        <button @click="remove(index)" class="btn btn-danger">Remove</button>
      </div>
    </div>
  </div>

</div>
</template>
<script>
import Answers from '../../components/Answers'

export default {
  name: "Create",
  data() {
    return {
      audio: {
        src: 'https://vignette.wikia.nocookie.net/leagueoflegends/images/5/5a/AhriStarGuardian.Attack03.ogg/revision/latest?cb=20171128014755',
        img: 'https://pa1.narvii.com/6523/f9c9f9becbc37e1cf7d3862e54a1b3e4ef88ecd2_128.gif',
        answered: false,
        hit: false,
        answers: [{
            text: 'A new moon is rising',
            correct: true
          },
          {
            text: 'A new moon is raising',
            correct: false
          },
          {
            text: 'A new doom is raising',
            correct: false
          },
          {
            text: 'A few moon is rising',
            correct: false
          }
        ],
        answered: false
      },
      answer: {
        text: '',
        correct: false
      }
    }
  },
  methods: {
    resetAudio() {
      this.exercise.audio = {
        src: '',
        img: '',
        answers: [],
        answered: false,
        hit: false
      }
    },
    addAudio(audio) {
      if (audio.answers.length > 0) {
        const validIndex = audio.answers.findIndex(x => x.correct)
        if (validIndex > -1) {
          this.$store.commit('addAudio', audio)
          this.$refs['answers'].setCurrent(this.exercise.audio)
          this.resetAudio()
        } else {
          alert('Correct answer must be selected!')
        }
      } else {
        alert('Answers must be informed!')
      }
    },
    addAnswer(answer) {
      this.audio.answers.push(answer)
      this.answer = {
        text: '',
        correct: false
      }
    },
    correctChange(answer) {
      this.audio.answers.map(x => {
        if (x.text === answer.text) x.correct = true
        else x.correct = false
      })
    },
    edit(audio, index) {
      this.audio = audio
      this.$store.commit('setToUpdate', index)
    },
    update(audio) {
      this.$store.commit('update', audio)
      this.cancelUpdate()
    },
    cancelUpdate() {
      this.$store.commit('setToUpdate', null)
      this.resetAudio()
    },
    remove(index) {
      this.$store.commit('remove', index)
    },
    removeAnswer(index) {
      this.audio.answers.splice(index, 1)
    }
  },
  computed: {
    exercise () {
      return this.$store.getters['getExercise']
    },
    isToUpdate () {
      return this.$store.getters['isToUpdate']
    }
  },
  components: {
    Answers
  },
  watch: {
    'exercise.audios': function (audios) {
      this.$refs[`answers`].setCurrent(audios)
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
