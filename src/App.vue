<template>
  <Piano :notes="notes"/>
</template>

<script>
import Piano from './components/Piano.vue'

export default {
  name: 'App',
  components: {
    Piano
  },
  data() {
    return {
      notes: [
          {note: "C4",  key: "Q",   type: "white",  obj: new Audio("assets/sounds/C4.mp3")},
          {note: "Db4", key: "2",   type: "black", obj: new Audio("assets/sounds/Db4.mp3")},
          {note: "D4",  key: "W",   type: "white close", obj: new Audio("assets/sounds/D4.mp3")},
          {note: "Eb4", key: "3",   type: "black", obj: new Audio("assets/sounds/Eb4.mp3")},
          {note: "E4",  key: "E",   type: "white close", obj: new Audio("assets/sounds/E4.mp3")},
          {note: "F4",  key: "R",   type: "white", obj: new Audio("assets/sounds/F4.mp3")},
          {note: "Gb4", key: "5",   type: "black", obj: new Audio("assets/sounds/Gb4.mp3")},
          {note: "G4",  key: "T",   type: "white close", obj: new Audio("assets/sounds/G4.mp3")},
          {note: "Ab4", key: "6",   type: "black", obj: new Audio("assets/sounds/Ab4.mp3")},
          {note: "A4",  key: "Y",   type: "white close", obj: new Audio("assets/sounds/A4.mp3")},
          {note: "Bb4", key: "7",   type: "black", obj: new Audio("assets/sounds/Bb4.mp3")},
          {note: "B4",  key: "U",   type: "white close", obj: new Audio("assets/sounds/B4.mp3")},
          {note: "C5",  key: "I",   type: "white", obj: new Audio("assets/sounds/C5.mp3")},
          {note: "Db5", key: "9",   type: "black", obj: new Audio("assets/sounds/Db5.mp3")},
          {note: "D5",  key: "O",   type: "white close", obj: new Audio("assets/sounds/D5.mp3")},
          {note: "Eb5", key: "0",   type: "black", obj: new Audio("assets/sounds/Eb5.mp3")},
          {note: "E5",  key: "P",   type: "white close", obj: new Audio("assets/sounds/E5.mp3")}
      ]
    }
  }
}
</script>

<style lang="sass">
html
  height: 100%
  width: 100%

body
  margin: 0
  padding: 0
  height: 100%
  display: flex
  justify-content: center
  align-items: center
  background: #000428
  background: -webkit-linear-gradient(to right, #000428, #004e92)
  background: linear-gradient(to right, #000428, #004e92)
  
</style>
