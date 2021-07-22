<template>
  <Piano :notes="notes" :createAnimation="createAnimation" :emitSound="emitSound" />
  <Midi
    :notes="notes"
    :createAnimation="createAnimation"
    :emitSound="emitSound"
    :midiTable="midiTable"
  />
</template>

<script>
import Piano from './components/Piano.vue'
import Midi from './components/Midi.vue'

export default {
  name: 'App',
  components: {
    Piano,
    Midi
  },
  data() {
    return {
      emitSound(audio) { // emit and slowly fades the sound away
        const clone = audio.cloneNode();
        clone.play();
        setTimeout(() => (clone.volume = 0.8), 400);
        setTimeout(() => (clone.volume = 0.6), 800);
        setTimeout(() => (clone.volume = 0.4), 1200);
        setTimeout(() => (clone.volume = 0.2), 1600);
        setTimeout(() => (clone.volume = 0), 2000);
      },
      createAnimation(notes, i, emitSound) {
        var currentKey = document.querySelector("#" + notes[i].note);
        emitSound(notes[i].obj);
        currentKey.classList.add("active"); // add a css class to create the illusion of playing through different animations 
        setTimeout(() => currentKey.classList.remove("active"), 200); // removing the class when the sound is emitted
      },
      notes: [{
        note: "C4",
        key: "Q",
        type: "white",
        obj: new Audio("assets/sounds/C4.mp3")
      },
      {
        note: "Db4",
        key: "W",
        type: "black",
        obj: new Audio("assets/sounds/Db4.mp3")
      },
      {
        note: "D4",
        key: "A",
        type: "white close",
        obj: new Audio("assets/sounds/D4.mp3")
      },
      {
        note: "Eb4",
        key: "E",
        type: "black",
        obj: new Audio("assets/sounds/Eb4.mp3")
      },
      {
        note: "E4",
        key: "S",
        type: "white close",
        obj: new Audio("assets/sounds/E4.mp3")
      },
      {
        note: "F4",
        key: "D",
        type: "white",
        obj: new Audio("assets/sounds/F4.mp3")
      },
      {
        note: "Gb4",
        key: "T",
        type: "black",
        obj: new Audio("assets/sounds/Gb4.mp3")
      },
      {
        note: "G4",
        key: "F",
        type: "white close",
        obj: new Audio("assets/sounds/G4.mp3")
      },
      {
        note: "Ab4",
        key: "Y",
        type: "black",
        obj: new Audio("assets/sounds/Ab4.mp3")
      },
      {
        note: "A4",
        key: "G",
        type: "white close",
        obj: new Audio("assets/sounds/A4.mp3")
      },
      {
        note: "Bb4",
        key: "U",
        type: "black",
        obj: new Audio("assets/sounds/Bb4.mp3")
      },
      {
        note: "B4",
        key: "H",
        type: "white close",
        obj: new Audio("assets/sounds/B4.mp3")
      },
      {
        note: "C5",
        key: "J",
        type: "white",
        obj: new Audio("assets/sounds/C5.mp3")
      },
      {
        note: "Db5",
        key: "O",
        type: "black",
        obj: new Audio("assets/sounds/Db5.mp3")
      },
      {
        note: "D5",
        key: "K",
        type: "white close",
        obj: new Audio("assets/sounds/D5.mp3")
      },
      {
        note: "Eb5",
        key: "P",
        type: "black",
        obj: new Audio("assets/sounds/Eb5.mp3")
      },
      {
        note: "E5",
        key: "L",
        type: "white close",
        obj: new Audio("assets/sounds/E5.mp3")
      }
      ],
      midiTable: [
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""]
      ]
    }
  }
}
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Encode+Sans+SC:wght@400;800&display=swap')

html
    height: 100%

body
    margin: 0
    padding: 0
    background: #5E00FF

@media screen and (max-width: 300px)
    #app
        display: none

    #smallestDisplay
        display: block!important
        color: white
        text-align: center
</style>
