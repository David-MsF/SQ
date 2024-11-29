<script setup>
import { ref, onMounted } from "vue";

import SekvenceSeQuick from "./components/SekvenceSeQuick.vue";
//zvuk,
import sound1Url from '@/assets/sounds/loop.wav';

//tečky
const zobrazeniKomponenty = ref(false);
// Počet teček 
const pocetTecek = ref(100);
// Aktuální index tečky
const aktualniIndex = ref(0);
// Třída pro animaci třesu tlačítka
const tridaTlacitka = ref('');

// Funkce pro určení, zda je tečka aktivní
const jeAktivni = (index) => {
  return index === aktualniIndex.value;
};

// Funkce pro spuštění načítání teček
const spustitNacitani = () => {
  setInterval(() => {
    aktualniIndex.value = (aktualniIndex.value + 2) % pocetTecek.value;
  }, 50);
};


// Funkce pro třesení tlačítka
const treseTlacitko = () => {
  tridaTlacitka.value = 'shake'; // Přidání třídy pro animaci
  setTimeout(() => {
    tridaTlacitka.value = ''; // Odebrání třídy po dokončení animace
  }, 500); // Délka animace
};

// Spuštění při montování komponenty
onMounted(() => {
  spustitNacitani(); // Spušťení načítání teček
  setInterval(treseTlacitko, 1000); // Třesení každé 3 sekundy
});



// zvuk
const sound1 = new Audio(sound1Url);
let zvukHraje = false;
sound1.loop = true;
const playSound1 = () => {
if (!zvukHraje) {
  sound1.play();
  zvukHraje = true;
}else {
  sound1.pause();
  sound1.currentTime = 0;
  zvukHraje = false;
}
};
sound1.volume = 0.5; // nastavení 50% hlasitosti

const setVolume = (volume) => {
  // Volume by mělo být mezi 0.0 a 1.0
  sound1.volume = volume;

};



</script>

<template>
  <div class="wrapper">
    <div class="hlavniNapis">
      <div class="hlava">
        <h1>SeQuick! v0.1</h1>
        <div class="dots">
          <span v-for="(dot, index) in pocetTecek" :key="index" :class="{ 'active': jeAktivni(index) }">•</span>
        </div>
        <button class="better" :class="tridaTlacitka" @click="playSound1" >Start your day better</button>
        <label for="volume">Nastavit hlasitost:</label>
        <input type="range" id="volume" min="0" max="1" step="0.1" @input="setVolume($event.target.value)" /> 
      </div>
    </div>

  </div>

  <!--<div class="sekvencer">

    <SekvenceSeQuick />

  </div> -->
</template>

<style scoped>
.hlava {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
 
}

.dots {
  font-size: 2rem;
}

.dots span {
  color: rgb(211, 31, 31);
  opacity: 0.3;
  transition: opacity 0s;
}

.dots span.active {
  opacity: 1;
}

h1 {
  position: relative;
  color: azure;
  font-family: Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
  font-size: 10rem;
  text-align: center;
}

.better {
  background-color: #814444;
  color: rgb(255, 255, 255);
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border: none;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(24, 9, 87, 0.2);
  transition: background-color 0.3s, transform 0.3s;
}

.better:hover {
  background-color: #ff0a0a;
  transform: scale(1.15);
}

.better:active {
  transform: scale(0.95);
}

/* Animace třesu */
@keyframes shake {
  0% {
    transform: translate(0);
  }

  25% {
    transform: translate(-4px, 0);
  }

  50% {
    transform: translate(4px, 0);
  }

  75% {
    transform: translate(-4px, 0);
  }

  100% {
    transform: translate(0);
  }
}

.shake {
  animation: shake 0.3s ease;
}
/* Responsivní úpravy */
@media (max-width: 600px) {
    h1 {
        font-size: 4rem; 
    }
    .dots {
        font-size: 1.5rem; 
    }
    .better {
        font-size: 14px; 
        padding: 8px 16px; 
    }
}

</style>