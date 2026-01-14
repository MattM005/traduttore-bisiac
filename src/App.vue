<script setup>
import { ref, watch } from 'vue';
import dizionarioCompleto from './json/termini.json';
import regoleTraduzione from './json/regole.json';

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

  // Applico le "regole"
  if (linguaSorgente.value === 'it') {
    regoleOrdinate.forEach(regola => {
      // RegEx ==> \b è "word boundary" (confine parola)
      const regex = new RegExp('\\b' + regola.it + '\\b', 'g');
      testoInLavorazione = testoInLavorazione.replace(regex, regola.bisiac);
    });
  }

  // Traduco la parola sul testo che rimane
  const parole = testoInLavorazione.split(/\s+/);

  const paroleFinali = parole.map(parola => {
    const parolaPulita = parola.replace(/[.,!?;:]/g, '');
    // ricerca parola in termini
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
    </div>
  </div>
</template>


<style scoped>

.title  {
  text-align: center;
  margin-top: 6rem;
}

.hero {
  min-height: 100vh;
  width: 100%;
  
}

.traduttore-container {
  max-width: 900px;
  margin: 2rem auto;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);

  display: flex;
  justify-content: space-between;
  flex-direction: row;
  gap: 4px;
}

.input-tr, .risultato-box {
  width: 50%;
}

.risultato {
  margin-top: 1rem;
  background-color: #f5f5f5;
  border-radius: 4px;
  min-height: 110px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.input-tr h2 {
  margin-top: 0;
}

.input-tr {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.input-tr input {
  width: 90%;
  height: 80px;
  border: none;
  text-align: center;
}

h2,
h3 {
  text-align: center;
}

textarea {
  width: 100%;
  max-width: 400px;
  padding: 10px;
  margin-top: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.selettore-lingua {
  margin-bottom: 1rem;
}

</style>