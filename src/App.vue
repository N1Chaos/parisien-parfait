<template>
  <div id="app">
    <header>
      <h1>Parisien Parfait</h1>
      <p>Teste ton niveau de parisianisme !</p>
    </header>

    <div v-if="currentQuestionIndex < questions.length">
      <div class="question">
        <h2>Question {{ currentQuestionIndex + 1 }} : {{ currentQuestion.text }}</h2>
        <div v-for="(option, index) in currentQuestion.options" :key="index" class="option">
          <label>
            <input type="radio" v-model="selectedAnswer" :value="index" />
            {{ option.text }} ({{ option.score }} pts)
          </label>
        </div>
        <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
        <button @click="nextQuestion">Suivant</button>
      </div>
    </div>

    <div v-else>
      <h2>Ton score : {{ score }} / {{ maxScore }}</h2>
      <p v-if="score >= 24" class="result snob">
        Snob du Marais : tu juges même les croissants !
      </p>
      <p v-else-if="score >= 15" class="result râleur">
        Râleur Sympa : un Parisien en devenir.
      </p>
      <p v-else class="result touriste">
        Touriste Perdu : Paris te snobe encore !
      </p>
      <button @click="resetQuiz">Recommencer</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const questions = ref([
  {
    text: 'Le métro est en grève. Que fais-tu ?',
    options: [
      { text: 'Râler bruyamment et tweeter #RATP.', score: 3 },
      { text: 'Prendre un café en terrasse, l’air blasé.', score: 2 },
      { text: 'Attendre patiemment (what ?).', score: 0 },
    ],
  },
  {
    text: 'Un touriste te demande la Tour Eiffel. Ta réponse ?',
    options: [
      { text: 'Soupirer et répondre en français rapide.', score: 3 },
      { text: 'Indiquer vaguement avec un sourire forcé.', score: 2 },
      { text: 'Expliquer en détail avec enthousiasme.', score: 0 },
    ],
  },
  {
    text: 'Ton croissant est trop cuit. Ta réaction ?',
    options: [
      { text: 'Le renvoyer avec un commentaire sarcastique.', score: 3 },
      { text: 'Manger en râlant intérieurement.', score: 2 },
      { text: 'Sourire et le manger quand même.', score: 0 },
    ],
  },
  {
    text: 'En terrasse, le service est lent. Que fais-tu ?',
    options: [
      { text: 'Claquer des doigts et soupirer bruyamment.', score: 3 },
      { text: 'Lancer un regard impatient au serveur.', score: 2 },
      { text: 'Attendre tranquillement en scrollant X.', score: 0 },
    ],
  },
  {
    text: 'Un vélo te coupe la route. Ta réponse ?',
    options: [
      { text: 'L’insulter avec un ‘T’es pas à Amsterdam !’', score: 3 },
      { text: 'Marmonner ‘C’est toujours pareil’.', score: 2 },
      { text: 'Ignorer et continuer ton chemin.', score: 0 },
    ],
  },
  {
    text: 'Il pleut à Paris. Ton réflexe ?',
    options: [
      { text: 'Râler contre la météo et ouvrir un parapluie chic.', score: 3 },
      { text: 'Mettre une capuche et grogner.', score: 2 },
      { text: 'Sourire, c’est romantique.', score: 0 },
    ],
  },
  {
    text: 'Un café coûte 5 €. Que dis-tu ?',
    options: [
      { text: '‘C’est du vol, mais c’est Saint-Germain.’', score: 3 },
      { text: 'Payer en silence, l’air blasé.', score: 2 },
      { text: 'Payer avec un grand sourire.', score: 0 },
    ],
  },
  {
    text: 'Tu vois un touriste en tongs. Ta pensée ?',
    options: [
      { text: '‘Quelle horreur, on n’est pas à la plage !’', score: 3 },
      { text: 'Lever les yeux au ciel.', score: 2 },
      { text: 'Ne rien remarquer.', score: 0 },
    ],
  },
  {
    text: 'La queue au musée est longue. Que fais-tu ?',
    options: [
      { text: 'Râler et chercher une entrée VIP.', score: 3 },
      { text: 'Attendre en critiquant l’organisation.', score: 2 },
      { text: 'Patienter calmement.', score: 0 },
    ],
  },
  {
    text: 'Tu rates ton métro d’une seconde. Et maintenant ?',
    options: [
      { text: 'Pester contre la RATP et tout le monde.', score: 3 },
      { text: 'Soupirer et checker ton téléphone.', score: 2 },
      { text: 'Rire et attendre le suivant.', score: 0 },
    ],
  },
]);

const currentQuestionIndex = ref(0);
const selectedAnswer = ref(null);
const score = ref(0);
const errorMessage = ref('');
const maxScore = computed(() => questions.value.length * 3);
const currentQuestion = computed(() => questions.value[currentQuestionIndex.value]);

const nextQuestion = () => {
  if (selectedAnswer.value === null) {
    errorMessage.value = 'Veuillez sélectionner une réponse !';
    return;
  }
  errorMessage.value = '';
  score.value += currentQuestion.value.options[selectedAnswer.value].score;
  selectedAnswer.value = null;
  console.log('Index:', currentQuestionIndex.value, 'Score:', score.value);
  currentQuestionIndex.value++;
};

const resetQuiz = () => {
  currentQuestionIndex.value = 0;
  score.value = 0;
  selectedAnswer.value = null;
  errorMessage.value = '';
};
</script>

<style scoped>
#app {
  font-family: 'Helvetica Neue', Arial, sans-serif;
  max-width: 700px;
  margin: 0 auto;
  padding: 20px;
  background: linear-gradient(to bottom, #f5f5f5, #ffffff);
}
header {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 20px;
}
header h1 {
  font-size: 2.5em;
  font-weight: bold;
  color: #e74c3c;
}
.question {
  background: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
}
.question h2 {
  font-size: 1.5em;
  color: #2c3e50;
  margin-bottom: 15px;
}
.option {
  margin: 10px 0;
  font-size: 1.1em;
}
.option label {
  display: block;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
}
.option input {
  margin-right: 10px;
}
button {
  background: #e74c3c;
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1.1em;
  transition: background 0.3s;
}
button:hover {
  background: #c0392b;
}
button:disabled {
  background: #ccc;
  cursor: not-allowed;
}
.error {
  color: #e74c3c;
  font-size: 0.9em;
  margin-top: 10px;
}
.result {
  font-size: 1.2em;
  font-weight: bold;
  margin: 20px 0;
}
.snob {
  color: #e74c3c;
}
.râleur {
  color: #3498db;
}
.touriste {
  color: #7f8c8d;
}
</style>