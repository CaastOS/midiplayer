<template>
  <div id="keyboard">
    <div id="keys">
      <div v-for="note in notes" :key="note" :class="note.type" :id="note.note" class="key"> <!-- keyboard creation loop -->
        {{ note.key }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Piano",
  props: {
    notes: {
      type: Array
    },
    emitSound: {
      type: Function
    },
    createAnimation: {
      type: Function
    }
  },
  mounted() {
    var notes = this.notes;
    var emitSound = this.emitSound;
    var createAnimation = this.createAnimation;
    var player = [];

    for (let i = 0; i < notes.length; i++) {

      player[i] = () => { // array of arrow functions where each index plays a note
        createAnimation(notes, i, emitSound)
      };

      document.querySelector("#" + notes[i].note).addEventListener("click", player[i]); // emit a note when the relative key is clicked
      window.addEventListener("keydown", function (event) {
        if (event.key.toLowerCase() == notes[i].key.toLowerCase() && !event.repeat) return player[i](); // emit a note when the relative key on physical keyboard is pressed
      });
    }
  },
};
</script>

<style scoped lang="sass">
  @use 'sass:math'
  // credits to Enteleform for the calculations!

  $keyboard_Width: 40vw
  $keyboard_Height: ($keyboard_Width * 0.3  )
  $border_Width: ($keyboard_Width * 0.004)
  $whiteKey_Count: 10
  $whiteKey_Height: $keyboard_Height
  $whiteKey_Width: math.div($keyboard_Width, $whiteKey_Count)
  $blackKey_Height: ($whiteKey_Height * 0.55)
  $blackKey_Width: ($whiteKey_Width  * 0.55)
  $blackKey_Offset: (- math.div($blackKey_Width, 1.8) - $border_Width)
  $fontSize: math.div($whiteKey_Width, 3)
  $whiteKey_Color: rgb(255,255,255)
  $blackKey_Color: rgb(0,0,0)
  $blackKey_BorderColor: rgb(0,0,0)
  $whiteKey_BorderColor: rgb(0,0,0)

  #keyboard
    opacity: 90%
    width: auto
    height: 17vw
    margin-top: 2vh
    display: inline-block
    margin-left: 2vw


  .key
    float: left
    position: relative
    display: inline-block
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

  .black
    padding-top: $blackKey_Height
    width: $blackKey_Width
    z-index: 2
    border: $border_Width solid $blackKey_BorderColor
    background-color: $blackKey_Color
    color: white

  .black, .close
    margin: 0 0 0 $blackKey_Offset

  .active
    transform: scale(0.98)
  
  .black.active
    background-color: lighten($blackKey_Color, 10%)
  
  .white.active
    background-color: darken($whiteKey_Color, 10%)

  #E5
    border-right: $border_Width solid $whiteKey_BorderColor

  @media screen and (max-width: 1145px)
    #keyboard
      width: auto
      margin-left: 0
      display: flex
      justify-content: center

</style>
