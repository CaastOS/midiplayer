<template>
  <div id="keyboard">
    <div v-for="note in notes" :key="note" :class="note.type" :id="note.note" class="key"> <!-- keyboard creation loop -->
      {{ note.key }}
    </div>
  </div>
</template>

<script>
export default {
  name: "Piano",
  props: ["notes"],
  mounted() {
      var notes = this.notes;
      var player = [];

      const emitSound = audio => { // emit and slowly fades the sound away
        const clone = audio.cloneNode();
        clone.play();
        setTimeout(() => (clone.volume = 0.8), 400);
        setTimeout(() => (clone.volume = 0.6), 800);
        setTimeout(() => (clone.volume = 0.4), 1200);
        setTimeout(() => (clone.volume = 0.2), 1600);
        setTimeout(() => (clone.volume = 0), 2000);
      }

      for(let i = 0; i < notes.length; i++) {
        player[i] = () => { // array of arrow functions where each index plays a note
          var currentKey =  document.querySelector("#" + notes[i].note);
          emitSound(notes[i].obj);
          currentKey.classList.add("active"); // add a css class to create the illusion of playing through different animations 
          setTimeout(() => currentKey.classList.remove("active"), 200); // removing the class when the sound is emitted
        };

        document.querySelector("#" + notes[i].note).addEventListener("click", player[i]); // emit a note when the relative key div is pressed
        window.addEventListener("keydown", function(event) {
          if(event.key.toLowerCase() == notes[i].key.toLowerCase() && !event.repeat) return player[i](); // emit a note when the relative key on your keyboard is pressed
        });
      }
  },
};
</script>

<style scoped lang="sass">

// credits to Enteleform for the calculations!

$keyboard_Width: 70vw
$keyboard_Height: ($keyboard_Width * 0.3  )
$border_Width: ($keyboard_Width * 0.004)
$whiteKey_Count: 10
$whiteKey_Height: $keyboard_Height
$whiteKey_Width: ($keyboard_Width / $whiteKey_Count)
$blackKey_Height: ($whiteKey_Height * 0.55)
$blackKey_Width: ($whiteKey_Width  * 0.55)
$blackKey_Offset: (-($blackKey_Width / 1.8) - $border_Width)
$fontSize: ($whiteKey_Width / 3)
$whiteKey_Color: rgb(255,255,255)
$blackKey_Color: rgb(0,0,0)
$blackKey_BorderColor: rgb(0,0,0)
$whiteKey_BorderColor: rgb(0,0,0)

html
  height: 100%


body
  height: 100%
  display: flex
  margin: auto
  justify-content: center
  align-items: center

#keyboard
  margin-top: -40vh
  opacity: 88%

.key
  float: left
  position: relative
  cursor: pointer
  text-align: center
  font-size: $fontSize

.white
  padding-top: $whiteKey_Height
  width: $whiteKey_Width
  z-index: 1
  border-top: $border_Width solid $whiteKey_BorderColor
  border-bottom: $border_Width solid $whiteKey_BorderColor
  border-left: $border_Width solid $whiteKey_BorderColor
  background-color: $whiteKey_Color
  color: black

.active
  background-color: darken($whiteKey_Color, 20%)
  transform: scale(0.98)

.black.active
  background: lighten($blackKey_Color, 20%)
  
.black
  padding-top: $blackKey_Height
  width: $blackKey_Width
  z-index: 2
  border: $border_Width solid $blackKey_BorderColor
  background-color: $blackKey_Color
  color: white

.black, .close
  margin: 0 0 0 $blackKey_Offset

#keyboard > div:nth-child(17)
  border-right: $border_Width solid $blackKey_BorderColor

</style>
