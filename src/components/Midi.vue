<template>
  <div id="controls">
    <button id="stop" disabled>stop</button>
    <button id="reset">reset</button>
    <button @click="playMidi" id="play">play</button>
    <label>Insert bpm:</label>
    <input type="number" id="tempo" placeholder="MIN: 1 - MAX: 300" />
    <label>Insert number of bars:</label>
    <input type="number" id="length" placeholder="MIN: 1 - MAX: 6" />
  </div>
  <div id="table">
    <div id="playable">
      <div class="column" id="notes">
        <div v-for="note in notes" :key="note" class="notes">{{ note.note }}</div>
      </div>
      <div v-for="(row, index) in midiTable" :key="row" :class="[`col-${index}`]" class="column">
        <div v-for="(column, index) in row" :key="column" :class="[`row-${index}`]" class="row">
          <span>○</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Piano",
  props: {
    notes: {
      type: Array,
    },
    midiTable: {
      type: Array,
    },
    emitSound: {
      type: Function,
    },
    createAnimation: {
      type: Function,
    },
  },
  mounted() {
    var notes = this.notes;
    var emitSound = this.emitSound;
    var createAnimation = this.createAnimation;
    var clickedColor = "rgb(219, 80, 74)"; // rgb color that will be displayed once a note is selected in the composer
    var grayedOut = "rgba(0, 0, 0, 0.4)";

    window.addEventListener("keydown", function (event) {
      // start and stop the composer with space
      if (event.key == " ") {
        if (document.querySelector("#stop").disabled) {
          document.querySelector("#play").click();
        } else {
          document.querySelector("#stop").click();
        }
      }
    });

    document.querySelector("#reset").addEventListener("click", function () {
      // Listen for click event on the reset button and reset table once clicked
      document.querySelectorAll(".row").forEach(function (element) {
        element.style.background = "none";
      });
    });

    this.midiTable.forEach((element, index) => {
      // creates a playable table based on the two-dimensional Array provided in App.vue
      var parentRow = ".col-" + index;
      for (let i = 0; i < element.length; i++) {
        var currentLocation = parentRow + " > " + "div.row-" + i + ".row";
        document.querySelector(currentLocation).addEventListener("click", function () {
          // Listen for click events on a cell, and add/remove the note based on content
          if (this.style.background !== clickedColor && this.parentNode.style.background !== grayedOut) {
            // if the content is in the chosen bars and it's not already selected, select it
            this.style.background = clickedColor;
            createAnimation(notes, i, emitSound);
          } else {
            // else, deselect it
            this.style.background = "none";
          }
        });
      }
    });
  },
  methods: {
    playMidi() {
      var notes = this.notes;
      var clickedColor = "rgb(219, 80, 74)";
      var midiTable = this.midiTable;
      var emitSound = this.emitSound;
      var grayedOut = "rgba(0, 0, 0, 0.4)";
      var createAnimation = this.createAnimation;
      let play = document.querySelector("#play");
      var stop = document.querySelector("#stop");

      document.querySelectorAll(".column").forEach((el) => {
        // reset the background of every COLUMN everytime that the button play is pressed, reason: grayed bars may change
        el.style.background = "none";
      });

      stop.disabled = false; // enables stop button to end the loop
      play.disabled = true; // disables play button, avoiding multiple cursors
      var bpm = document.getElementById("tempo").value;
      var length = document.getElementById("length").value;

      // If values are higher or lower than those stated in the placeholder, set them to the limit
      if (bpm < 1) bpm = 1;
      if (bpm > 300) bpm = 300;
      if (length < 1) length = 1;
      if (length >= midiTable.length / 4) length = midiTable.length / 4;

      // Gray out columns out of bar range and delete every note previously inserted there
      for (let i = length * 4; i < midiTable.length; i++) {
        let column = document.querySelector(".col-" + i);
        column.style.background = grayedOut;
        column.children.forEach((el) => {
          el.style.background = "none";
        });
      }

      var tempo = ((60 / bpm) * 1000) / 4; // divide the table in 4/4 and convert seconds in milliseconds
      var abort = false;
      /* eslint-disable no-unused-vars */
      const startMidi = new Promise(function midiPromise() {

        for (let j = 0; j < length * 4; j++) {
          // loop the table for the selected length (in quarters)
          setTimeout(function () {
            // set timeout to respect tempo
            for (let i = 0; i < midiTable[0].length; i++) {
              // for every element contained in midiTable (since all the content is the same [0] will do)

              if (abort) return; // if the button stop has been clicked, return

              var currentCell = document.querySelector(
                ".col-" + j + " > " + "div.row-" + i + ".row"
              );
              document.querySelectorAll(".col-" + j + "> .row").forEach((el) => {
                // create a moving cursor
                el.classList.add("select");
                setTimeout(() => el.classList.remove("select"), tempo);
              });

              if (currentCell.style.background == clickedColor) {
                createAnimation(notes, i, emitSound); // emits the sound based on whether the table was clicked or not
              }
            }

            if (j == length * 4 - 1) setTimeout(() => midiPromise(), tempo); // when the table has been looped and stop hasn't been pressed, reloop it (and wait for the last 1/4 beat)

            stop.addEventListener("click", function () {
              // if the stop button has been pressed, abort = true and re-enable play
              stop.disabled = true;
              play.disabled = false;
              abort = true;
            });
          }, j * tempo);
        }
      });
      /* eslint-enable no-unused-vars */
    },
  },
};
</script>

<style scoped lang="sass">
@use 'sass:math'

$fontFamily: 'Encode Sans SC', sans-serif
$beats: 6
$notes: 17

#controls
    text-align: center
    width: 40vw
    margin-left: 2vw

button, label, input
    font-family: $fontFamily

button
    display: inline-block
    background: white
    border: 1px solid black
    padding: 0.3rem 1rem
    margin: 0 10px 15px 10px
    border-radius: 10px
    cursor: pointer
    font-size: 0.9rem
    font-weight: 800

button:hover:enabled
    color: white
    border: 1px solid #db504a
    transform: scale(1.02)
    transition: 0.25s all
    background: #db504a

label
    color: white
    display: block

input[type=number]
    text-align: center
    width: 140px
    border: solid 3px transparent
    margin: 15px 0
    border-radius: 15px

input[type=number]:focus,input[type=number]:hover
    outline: none
    transform: scale(1.05)
    transition: 0.3s all

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button
    -webkit-appearance: none
    margin: 0

.notes
    color: white
    cursor: default
    padding: 0 3px
    padding-top: 0.611rem
    border-bottom: none
    font-size: 1.1rem

#playable
    position: absolute
    top: 2vh
    right: 2vw
    font-size: 1.5rem
    text-align: center
    font-family: $fontFamily
    padding-bottom: 5vh

.column
    display: inline-block
    border: 1px solid black
    border-right: 1px solid rgba(0,0,0,0.2)

.row
    cursor: pointer
    color: rgba(0,0,0,0)
    padding: 0 0.3vw
    border-bottom: 1px solid rgba(0,0,0,0.2)

.row > span
    visibility: hidden

.select
    background: rgba(230,0,0,0.5)!important

$selectedCell: -1
@while $selectedCell < ($beats * 4) // adds thick black border at the end of each 4/4 beat
    #playable > div.col-#{($selectedCell + 4)}.column
        border-right: 3px solid black
    $selectedCell: $selectedCell + 4

#notes
    border: 2px solid rgba(0,0,0,0.8)
    div:nth-child(#{$notes})
        padding-bottom: 2px!important

// Responsive queries

@media screen and (min-width: 1720px)
    #playable
        font-size: 2.5rem

    .notes
        padding-top: 1.29rem
        font-size: 1.5rem

@media screen and (max-width: 1145px)
    #playable
        position: relative
    #controls
        width: auto
        margin-left: 0
    #table
        text-align: center
        padding-left: 1.1rem

@media screen and (max-width: 565px)
    #playable
        font-size: 1.3rem

    .notes
        padding-top: 0.548rem
        font-size: 0.9rem

@media screen and (max-width: 503px)
    #playable
        font-size: 0.8rem

    .notes
        padding-top: 0.304rem
        font-size: 0.6rem

@media screen and (max-width: 340px)
    #playable
        font-size: 0.62rem

    .notes
        padding-top: 0.305rem
        font-size: 0.4rem
</style>
