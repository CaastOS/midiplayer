<h1 align="center"> MidiPlayer </h1>
<img src="https://i.imgur.com/wCPMHp1.jpg">
<hr>

<h2 align="center"> Introduction </h2>
<p align="center">MidiPlayer is a web application created in <a href="https://vuejs.org/">Vue.js</a>, <a href="https://www.javascript.com/">Javascript</a> and <a href="https://sass-lang.com/">SASS</a>. It's a fully functional and responsive piano DAW (digital audio workstation), namely a way to compose digital music. try it yourself <a href="https://midiplayer.claudiocastorina.com">here</a>!</p><br>

<h2 align="center"> Usage </h2>
Before you start composing your melody, set the bpm and the number of beats (each beat is highlighted with a dark border). Once this is done, click on the table to select the notes (1.5 octaves) and create your melody! Once composed, press 'Play' (or press space) to play the loop. To stop it, you can press 'Stop', or simply press Space again. The reset button is there in case want to delete the entire melody.<br>
<h2 align="center"> Components </h2>

<p> The application consists of App.vue and two other components: Piano and Midi. </p>

<h3> App.vue </h3>
App.vue is the parent of the two components, in which various props are declared and passed:<br><br>
-createAnimation, the main function, used to emit the required sound and create an animation in the virtual keyboard.<br>
-midiTable, a two-dimensional array which will then become the table where the melodies are composed.<br>
-notes, which is essentially an array of objects where all the data related to the music notes are inserted.<br>
-emitSound, modifies the .mp3 sound so that it decreases in volume, and then plays it.<br>
<h3> Piano.vue </h3>

This is the component where the piano is created along with all the logic needed to play it (either with the keyboard or by clicking it).<br>

<h3> Midi.vue </h3>

This component is where the actual DAW is born. The table is created by looping all the elements of the MidiTable array, where each element corresponds to a cell. Then all the logic related to the buttons, the click of the cells and the realization of the musical time is created. Once this is done, the midiPromise function is executed, and will remain in the loop until the stop button is pressed.

<hr>
<h2 align="center"> License </h2>
<h3 align="center"><a href="https://choosealicense.com/licenses/mit/" target="_blank"> MIT </a></h3>
