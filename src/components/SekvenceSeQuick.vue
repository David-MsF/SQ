<script setup>
import { ref } from 'vue';

// Import zvuků
/*
const sound1 = new Audio(require('@/assets/sounds/Kick.wav'));
const sound2 = new Audio(require('@/assets/sounds/hihat.wav'));
const sound3 = new Audio(require('@/assets/sounds/Snare.wav'));

// Můžu také zvuky uložit do pole nebo objektu, pokud máte více zvuků
const sounds = {
  sound1,
  sound2,
};

// Případně si můžu definovat funkce pro přehrávání zvuků
const playSound1 = () => {
  sound1.play();
};

const playSound2 = () => {
  sound2.play();
};
*/

/*import sound2Url from '@/assets/sounds/Snare.wav'; */


/*const sound2 = new Audio(sound2Url); */

const playSound1 = () => {
    sound1.play();
};

const playSound2 = () => {
    sound2.play();
};
</script>



<template>

    <div>
        <button @click="playSound1">Přehrát zvuk 1</button>
        <button @click="playSound2">Přehrát zvuk 2</button>
    </div>

</template>




<style scoped></style>