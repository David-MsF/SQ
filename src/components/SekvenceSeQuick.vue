<script setup>
import { ref } from 'vue';
import { Midi } from '@tonejs/midi';

import kopakSound from '@/assets/sounds/kick.wav';
import hihatSound from '@/assets/sounds/hihat.wav';
import snareSound from '@/assets/sounds/snare.wav';

const kopakAudio = new Audio(kopakSound);
const hihatAudio = new Audio(hihatSound);
const snareAudio = new Audio(snareSound);

const kopak = ref(Array(16).fill(false));
const snare = ref(Array(16).fill(false));
const hiHat = ref(Array(16).fill(false));
const tempo = ref(120);
const hraje = ref(false);
const aktualniKrok = ref(0);
let intervalId;

const hraj = () => {
  if (hraje.value) return;

  hraje.value = true;
  const krokDoba = (60 / tempo.value) * 1000 / 4; // 4 je, protože hrajeme na 16 dob

  intervalId = setInterval(() => {
    if (kopak.value[aktualniKrok.value]) {
      kopakAudio.currentTime = 0; // Restart zvuku
      kopakAudio.play();
    }
    if (snare.value[aktualniKrok.value]) {
      snareAudio.currentTime = 0; // Restart zvuku
      snareAudio.play();
    }
    if (hiHat.value[aktualniKrok.value]) {
      hihatAudio.currentTime = 0; // Restart zvuku
      hihatAudio.play();
    }

    aktualniKrok.value = (aktualniKrok.value + 1) % 16; // Pokračuje na další krok
  }, krokDoba);
};

const zastav = () => {
  clearInterval(intervalId);
  hraje.value = false;
  aktualniKrok.value = 0; // Resetuju aktuální krok na začátek
};

const prepniZvuk = (zvukArray, index) => {
  zvukArray[index] = !zvukArray[index];
};

const createMidi = () => {
  const midi = new Midi();
  const track = midi.addTrack();

  // Přidání not do MIDI podle uživatelských výběrů
  for (let i = 0; i < 16; i++) {
    if (kopak.value[i]) {
      track.addNote({
        midi: 36, // midi číslo pro kopák
        time: i * (60 / tempo.value / 4), // čas v sekundách
        duration: 0.5 // délka noty
      });
    }
    if (snare.value[i]) {
      track.addNote({
        midi: 38, // midi číslo pro snare
        time: i * (60 / tempo.value / 4),
        duration: 0.5
      });
    }
    if (hiHat.value[i]) {
      track.addNote({
        midi: 42, // midi číslo pro hi-hat
        time: i * (60 / tempo.value / 4),
        duration: 0.5
      });
    }
  }

  // Uložení a stažení MIDI souboru
  const midiData = midi.toArray();
  const blob = new Blob([midiData], { type: 'audio/midi' });
  const url = URL.createObjectURL(blob);

  const a = document.createElement('a');
  a.href = url;
  a.download = 'user-pattern.mid';
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

// Počet teček 
const pocetTecek = ref(16);
const aktualniIndex = ref(0);
const jeAktivni = (index) => {
  return index === aktualniKrok.value;
};
</script>

<template>
  <div class="container">
    <h1>Easy MsF Pattern</h1>

    <label>
      Rychlost (BPM):
      <input type="number" v-model="tempo" min="10" max="250" />
    </label>
    <div class="controls">
      <button @click="hraj" :disabled="hraje">Přehraj</button>
      <button @click="zastav" :disabled="!hraje">Zastav</button>
      <button @click="createMidi">Stáhnout MIDI</button> <!-- Tlačítko pro stažení MIDI -->
    </div>
    <div class="dots">
      <span v-for="(dot, index) in pocetTecek" :key="index" :class="{ 'active': jeAktivni(index) }">•</span>
    </div>

    <div class="sound-grid">
      <div class="sound-row">
        <div class="sound-title">Kopák</div>
        <div class="sound-section">
          <div v-for="(value, index) in kopak" :key="'kopak-' + index">
            <label>
              <input type="checkbox" :checked="value" @change="prepniZvuk(kopak, index)" />
            </label>
          </div>
        </div>
      </div>

      <div class="sound-row">
        <div class="sound-title">Snare</div>
        <div class="sound-section">
          <div v-for="(value, index) in snare" :key="'snare-' + index">
            <label>
              <input type="checkbox" :checked="value" @change="prepniZvuk(snare, index)" />
            </label>
          </div>
        </div>
      </div>

      <div class="sound-row">
        <div class="sound-title">HiHat</div>
        <div class="sound-section">
          <div v-for="(value, index) in hiHat" :key="'hihat-' + index">
            <label>
              <input type="checkbox" :checked="value" @change="prepniZvuk(hiHat, index)" />
            </label>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  text-align: center;
  margin: 20px;
  position: relative;
  color: aqua;
}

h1 {
  font-size: 24px;
  margin-bottom: 16px;
}

.sound-grid {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.sound-row {
  display: flex;
  align-items: center;
  margin: 10px 0;
}

.sound-title {
  font-weight: bold;
  margin-right: 10px;
}

.sound-section {
  display: flex;
}

label {
  margin: 0 2px;
}

.controls {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}

button {
  margin: 0 10px;
}

.dots {
  font-size: 2rem;
}

.dots span {
  color: red;
  opacity: 0.3;
  transition: opacity 0.2s ease;
}

.dots span.active {
  opacity: 1;
}
</style>