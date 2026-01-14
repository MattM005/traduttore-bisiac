<script setup>
import { ref, watch } from 'vue';
import dizionarioCompleto from './json/termini.json';
import regoleTraduzione from './json/regole.json';
import Foot from './components/Footer.vue'

const dizionarioArray = Object.values(dizionarioCompleto);
// ordinamento crescente delle parole (ottimizz.)
const regoleOrdinate = regoleTraduzione.sort((a, b) => b.it.length - a.it.length);
const testoInput = ref('');
const testoTradotto = ref('');
const linguaSorgente = ref('it');

const traduciTesto = (testo) => {
  if (!testo.trim()) {
    return '';
  }

  let testoInLavorazione = testo.toLowerCase();

  // Applico "regole"
  if (linguaSorgente.value === 'it') {
    regoleOrdinate.forEach(regola => {
      // RegEx ==> \b (confine parola)
      const regex = new RegExp('\\b' + regola.it + '\\b', 'g');
      testoInLavorazione = testoInLavorazione.replace(regex, regola.bisiac);
    });
  }

  const parole = testoInLavorazione.split(/\s+/);

  const paroleFinali = parole.map(parola => {
    const parolaPulita = parola.replace(/[.,!?;:]/g, '');
    const voceDizionario = dizionarioArray.find(voce =>
      voce.it.some(traduzioneIt => traduzioneIt.toLowerCase() === parolaPulita)
    );
    // trovo corrispondenze
    if (voceDizionario) {
      return voceDizionario.bisiac;
    } else {
      return parola;
    }
  });

  return paroleFinali.join(' ');
};

watch([testoInput, linguaSorgente], ([nuovoTesto]) => {
  testoTradotto.value = traduciTesto(nuovoTesto);
});
</script>


<template>
  <div>
    <div class="hero">
      <h1 class="title">Tradutor Bisiac</h1>
      <div class="traduttore-container">
        <div class="input-tr">
          <h2>Testo da Tradur</h2>
          <!-- Selettore della lingua -->
          <!--<div class="selettore-lingua">
            <label>Traduci da:</label>
            <select v-model="linguaSorgente">
              <option value="it">Italiano</option>
              <option value="en">Inglese</option>
            </select>
          </div>-->

          <!-- Area di testo per l'input -->
          <input v-model="testoInput" placeholder="Scrivi qui el testo da tradur..." id="textarea"
            rows="5"></input>
        </div>

        <!-- Area dove mostrare il risultato -->
        <div class="risultato-box">
          <h2>Traduzion:</h2>
          <div class="risultato">
            <p>{{ testoTradotto }}</p>
          </div>
        </div>
      </div>
      <Foot />
    </div>
  </div>
</template>