<template>
  <div id="controls">
    <button id="stop" disabled> stop </button>
    <button id="reset"> reset </button> 
    <button @click="playMidi" id="play"> play </button>
    <label>Insert bpm:</label>
    <input type="number" id="tempo" placeholder="MIN: 1 - MAX: 300">
  </div>
  <div id="table">
  <div id="playable">
    <div class="column" id="notes">
    <div v-for="note in notes" :key="note" class="notes">{{note.note}}</div>
    </div>
    <div v-for="(row, index) in midiTable" :key="row" :class="[`col-${index}`]" class="column">
      <div v-for="(column, index) in row" :key="column" :class="[`row-${index}`]" class="row"><span>○</span></div>
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
    midiTable: {
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
    var clickedColor = "rgb(219, 80, 74)";
    window.addEventListener("keydown", function (event) { // start and stop the composer with space
      if(event.key == " ") {
        if(document.querySelector("#stop").disabled) {
          document.querySelector("#play").click();
        } else {
          document.querySelector("#stop").click();
        }
      }
    });

    document.querySelector("#reset").addEventListener("click", function () { // Listen for click event on the reset button and reset table once clicked
      document.querySelectorAll('.row').forEach(function(element) {
        element.style.background = "none";
      });
    });
    this.midiTable.forEach((element, index) => { // creates a table based on the two-dimensional Array provided in App.vue
      var parentRow = ".col-" + index;
      for (let i = 0; i < element.length; i++) {
        var currentLocation = parentRow + " > " + "div.row-" + i + ".row";
        document.querySelector(currentLocation).addEventListener("click", function () { // Listen for click events on the table, and add/remove the note based on content
          if (this.style.background !== clickedColor) { // if the content's background is already rgb(219, 80, 74), set it to none; else, set it to rgb(219, 80, 74)
            this.style.background = clickedColor;
            createAnimation(notes, i, emitSound);
          } else {
            this.style.background = "none";
          }
        });
      }
    });
  },
  methods: {
    playMidi() {
      var abort = false;
      var notes = this.notes;
      var clickedColor = "rgb(219, 80, 74)";
      var midiTable = this.midiTable;
      var emitSound = this.emitSound;
      var createAnimation = this.createAnimation;
      let play = document.querySelector("#play");
      var stop = document.querySelector("#stop");
      stop.disabled = false; // enables stop button to end the loop
      play.disabled = true; // disables play button, avoiding multiple cursors
      let startMidi = new Promise((resolve) => {
        midiTable.forEach((element, index) => { // for each column in midiTable
          var bpm = document.getElementById('tempo').value;
          if (bpm < 1) bpm = 1;
          if (bpm > 300) bpm = 300;
          var tempo = (60 / bpm) * 1000 / 4; // divide the table in 4/4 and convert seconds in milliseconds
          setTimeout(function() {
            for (let i = 0; i < element.length; i++) {
              var currentCell = document.querySelector(".col-" + index + " > " + "div.row-" + i + ".row");
              if(!abort) {
                document.querySelectorAll(".col-" + index + "> .row").forEach(el => { // create a moving cursor
                    el.classList.add("select"); 
                    setTimeout(() => el.classList.remove("select"), tempo);
                })
                if (currentCell.style.background == clickedColor) {
                    createAnimation(notes, i, emitSound) // emits the sound based on whether the table was clicked or not
              }
              }

            }
            if (index == midiTable.length - 1) resolve(); // when the table has been looped, resolve the Promise
            stop.addEventListener("click", function() { // if the stop button has been pressed, resolve the Promise
                abort = true;
                resolve();
            });
          }, index * tempo);
          
        });
        
      });
      startMidi.then(() => { // if the stop button has been pressed, stop the loop; if the stop button hasn't been pressed, continue
        if (!abort) {
          play.disabled = false;
          play.click();
          play.disabled = true;
        } else {
          play.disabled = false;
          stop.disabled = true;
        }
      });
    }
  }
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
    transform:  scale(1.02)
    transition: 0.25s all
    background: #db504a

  label
    color: white
    display: block

  input[type=number]
    text-align: center
    width: 140px
    border: solid 3px transparent
    margin-top: 15px
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
    div:nth-child(#{$notes})
      padding-bottom: 2px!important
    border: 2px solid rgba(0,0,0,0.8)

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
