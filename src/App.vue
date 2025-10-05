<template>
  <v-app>
    <v-main>
      <v-container fluid class="paris-bg">
        <v-row justify="center" align="center" style="min-height: 100vh;">
          <v-col cols="12" sm="10" md="8" lg="6">
            <v-sheet elevation="6" class="pa-6 paris-sheet" rounded="xl">
              <v-row justify="center">
                <v-col cols="12" class="text-center animate-title">
                  <h1 class="paris-title">Parisien Parfait</h1>
                  <p class="paris-subtitle">Teste ton niveau de parisianisme !</p>
                </v-col>
              </v-row>
              
              <v-progress-linear
                :model-value="progress"
                color="blue-darken-2"
                height="8"
                rounded
                class="mb-4"
              ></v-progress-linear>
              
              <transition name="fade" mode="out-in">
                <div v-if="currentQuestionIndex < questions.length" key="question">
                  <v-sheet elevation="2" class="question-sheet pa-4" rounded="lg">
                    <h2 class="paris-question">
                      Question {{ currentQuestionIndex + 1 }} : {{ currentQuestion.text }}
                    </h2>
                    
                    <div class="options-container mt-4">
                      <v-btn
                        v-for="(option, index) in currentQuestion.options"
                        :key="index"
                        :class="['option-btn', selectedAnswer === index ? 'option-selected' : 'option-default']"
                        variant="outlined"
                        block
                        height="auto"
                        min-height="60px"
                        @click="selectAnswer(index)"
                      >
                        <span class="paris-option">
  <span class="option-text">{{ option.text }}</span>
  <span class="score">({{ option.score }} pts)</span>
</span>
                      </v-btn>
                    </div>
                    
                    <v-alert 
                      v-if="errorMessage" 
                      type="error" 
                      density="compact" 
                      class="ma-2" 
                      variant="outlined"
                    >
                      {{ errorMessage }}
                    </v-alert>
                    
                    <v-btn
                      color="red-darken-2"
                      variant="flat"
                      rounded
                      @click="nextQuestion"
                      :disabled="selectedAnswer === null"
                      class="mt-4 next-btn"
                      size="large"
                      block
                    >
                      <v-icon start>mdi-arrow-right</v-icon>
                      Suivant
                    </v-btn>
                  </v-sheet>
                </div>
                
                <div v-else key="result">
                  <v-sheet elevation="2" class="result-sheet pa-4" rounded="lg">
                    <h2 class="paris-result">Ton score : {{ score }} / {{ maxScore }}</h2>
                    <p v-if="score >= 36" class="snob">
                      <v-icon color="red-darken-2" start>mdi-crown</v-icon>
                      Snob du Marais : tu juges même les croissants !
                    </p>
                    <p v-else-if="score >= 18" class="râleur">
                      <v-icon color="blue-darken-2" start>mdi-coffee</v-icon>
                      Râleur Sympa : un Parisien en devenir.
                    </p>
                    <p v-else class="touriste">
                      <v-icon color="grey-darken-1" start>mdi-map-marker</v-icon>
                      Touriste Perdu : Paris te snobe encore !
                    </p>
                    
                    <div class="mt-4 d-flex flex-wrap gap-2 justify-center">
                      <v-btn 
                        color="red-darken-2" 
                        variant="flat" 
                        rounded 
                        @click="resetQuiz"
                        size="large"
                        class="action-btn"
                      >
                        <v-icon start>mdi-replay</v-icon>
                        Recommencer
                      </v-btn>
                      <v-btn
                        color="blue-darken-2"
                        variant="outlined"
                        rounded
                        :href="shareUrl"
                        target="_blank"
                        size="large"
                        class="action-btn"
                      >
                        <v-icon start>mdi-twitter</v-icon>
                        Partager sur X
                      </v-btn>
                    </div>
                  </v-sheet>
                </div>
              </transition>
            </v-sheet>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { ref, computed } from 'vue';

export default {
  name: 'ParisienQuiz',
  
  setup() {
    const questions = ref([
      {
        text: 'Le métro est en grève. Que fais-tu ?',
        options: [
          { text: 'Râler bruyamment et tweeter #RATP.', score: 3 },
          { text: 'Prendre un café en terrasse, l\'air blasé.', score: 2 },
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
          { text: 'L\'insulter avec un "T\'es pas à Amsterdam !"', score: 3 },
          { text: 'Marmonner "C\'est toujours pareil".', score: 2 },
          { text: 'Ignorer et continuer ton chemin.', score: 0 },
        ],
      },
      {
        text: 'Il pleut à Paris. Ton réflexe ?',
        options: [
          { text: 'Râler contre la météo et ouvrir un parapluie chic.', score: 3 },
          { text: 'Mettre une capuche et grogner.', score: 2 },
          { text: 'Sourire, c\'est romantique.', score: 0 },
        ],
      },
      {
        text: 'Un café coûte 5 €. Que dis-tu ?',
        options: [
          { text: '"C\'est du vol, mais c\'est Saint-Germain."', score: 3 },
          { text: 'Payer en silence, l\'air blasé.', score: 2 },
          { text: 'Payer avec un grand sourire.', score: 0 },
        ],
      },
      {
        text: 'Tu vois un touriste en tongs. Ta pensée ?',
        options: [
          { text: '"Quelle horreur, on n\'est pas à la plage !"', score: 3 },
          { text: 'Lever les yeux au ciel.', score: 2 },
          { text: 'Ne rien remarquer.', score: 0 },
        ],
      },
      {
        text: 'La queue au musée est longue. Que fais-tu ?',
        options: [
          { text: 'Râler et chercher une entrée VIP.', score: 3 },
          { text: 'Attendre en critiquant l\'organisation.', score: 2 },
          { text: 'Patienter calmement.', score: 0 },
        ],
      },
      {
        text: 'Tu rates ton métro d\'une seconde. Et maintenant ?',
        options: [
          { text: 'Pester contre la RATP et tout le monde.', score: 3 },
          { text: 'Soupirer et checker ton téléphone.', score: 2 },
          { text: 'Rire et attendre le suivant.', score: 0 },
        ],
      },
      {
        text: 'Un pigeon te vise. Ta réaction ?',
        options: [
          { text: 'Jurer : "Sale bête parisienne !"', score: 3 },
          { text: 'Esquiver avec agilité.', score: 2 },
          { text: 'Rire et continuer.', score: 0 },
        ],
      },
      {
        text: 'Une expo est complète. Ta réaction ?',
        options: [
          { text: 'Râler : "Typique, Paris fait exprès !"', score: 3 },
          { text: 'Checker une autre expo sur ton appli.', score: 2 },
          { text: 'Sourire et visiter un parc.', score: 0 },
        ],
      },
    ]);

    const currentQuestionIndex = ref(0);
    const selectedAnswer = ref(null);
    const score = ref(0);
    const errorMessage = ref('');

    const maxScore = computed(() => questions.value.length * 3);
    const progress = computed(() => (currentQuestionIndex.value / questions.value.length) * 100);
    const currentQuestion = computed(() => questions.value[currentQuestionIndex.value]);

    const shareUrl = computed(() => {
      const result = score.value >= 36 ? 'Snob du Marais' : score.value >= 18 ? 'Râleur Sympa' : 'Touriste Perdu';
      return `https://twitter.com/intent/tweet?text=Je suis un ${result} au Parisien Parfait ! Score : ${score.value}/${maxScore.value}`;
    });

    const selectAnswer = (index) => {
      selectedAnswer.value = index;
      errorMessage.value = '';
    };

    const nextQuestion = () => {
      if (selectedAnswer.value === null) {
        errorMessage.value = 'Veuillez sélectionner une réponse !';
        return;
      }
      errorMessage.value = '';
      score.value += currentQuestion.value.options[selectedAnswer.value].score;
      selectedAnswer.value = null;
      currentQuestionIndex.value++;
    };

    const resetQuiz = () => {
      currentQuestionIndex.value = 0;
      score.value = 0;
      selectedAnswer.value = null;
      errorMessage.value = '';
    };

    return {
      questions,
      currentQuestionIndex,
      selectedAnswer,
      score,
      errorMessage,
      maxScore,
      progress,
      currentQuestion,
      shareUrl,
      selectAnswer,
      nextQuestion,
      resetQuiz
    };
  }
}
</script>

<style scoped>
.paris-bg {
  background: linear-gradient(rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.4)), url('/assets/paris-bg-CnVnKUmc.jpg') no-repeat center/cover fixed;
  min-height: 100vh;
  padding: 16px 12px;
  background-color: #1E3A8A;
}

.paris-sheet {
  background: rgba(255, 253, 208, 0.95);
  border-radius: 16px;
}

.paris-title {
  color: #8B0000;
  font-family: 'Playfair Display', serif;
  font-weight: 700;
  font-size: 2.5rem;
}

.paris-subtitle {
  color: #1E3A8A;
  font-family: 'Lora', serif;
  font-size: 1.2rem;
  margin-bottom: 16px;
}

.paris-question {
  color: #2c3e50;
  font-family: 'Lora', serif;
  font-size: 1.5rem;
  text-align: center;
  line-height: 1.3;
}

.paris-option {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  text-align: center;
  padding: 0 4px;
}

.option-text {
  display: block;
  margin-bottom: 4px;
  line-height: 1.4;
  word-wrap: break-word;
  width: 100%;
  box-sizing: border-box;
}

.score {
  color: #8B0000;
  font-size: 0.9rem;
  white-space: nowrap;
}

.question-sheet {
  background: #FFFDD0;
  border-left: 6px solid #8B0000;
}

.result-sheet {
  background: #FFFDD0;
  border-left: 6px solid #1E3A8A;
}

.paris-result {
  color: #2c3e50;
  font-size: 1.5rem;
  margin-bottom: 20px;
  text-align: center;
  font-family: 'Lora', serif;
}

.snob {
  color: #8B0000;
  font-family: 'Lora', serif;
  font-weight: bold;
  font-size: 1.4rem;
  text-align: center;
}

.râleur {
  color: #1E3A8A;
  font-family: 'Lora', serif;
  font-weight: bold;
  font-size: 1.4rem;
  text-align: center;
}

.touriste {
  color: #4B5563;
  font-family: 'Lora', serif;
  font-weight: bold;
  font-size: 1.4rem;
  text-align: center;
}

.next-btn, .action-btn {
  text-transform: none;
  font-family: 'Lora', serif;
  font-size: 1rem;
}

.options-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.option-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: none;
  padding: 16px 8px;
  border: 2px solid #e0e0e0;
  transition: all 0.3s ease;
  font-family: 'Lora', serif;
  cursor: pointer;
  background-color: white;
  min-height: 90px;
  width: 100%;
  box-sizing: border-box;
}

.option-default {
  background-color: white;
  color: #2c3e50;
  border: 2px solid #e0e0e0;
}

.option-selected {
  background-color: #8B0000 !important;
  color: white !important;
  border: 2px solid #8B0000 !important;
}

.option-btn:hover {
  border-color: #8B0000;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.animate-title {
  animation: fadeIn 1s ease-in;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-20px); }
  to { opacity: 1; transform: translateY(0); }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.gap-2 {
  gap: 8px;
}

.d-flex {
  display: flex;
}

.flex-wrap {
  flex-wrap: wrap;
}

.justify-center {
  justify-content: center;
}

/* CORRECTIONS POUR MOBILE */
@media (max-width: 768px) {
  .paris-bg {
    padding: 12px 8px;
  }
  
  .paris-sheet {
    margin: 0 4px;
  }
  
  .paris-title {
    font-size: 2rem;
    line-height: 1.2;
    padding: 0 8px;
  }
  
  .paris-subtitle {
    font-size: 1.1rem;
    margin-bottom: 12px;
    padding: 0 8px;
  }
  
  .paris-question {
    font-size: 1.3rem;
    line-height: 1.3;
    margin-bottom: 16px;
    padding: 0 8px;
  }
  
  .options-container {
    gap: 10px;
    padding: 0 4px;
  }
  
  .option-btn {
    min-height: 100px;
    padding: 12px 6px;
    border-radius: 8px;
    margin: 0 2px;
  }
  
  .paris-option {
    flex-direction: column;
    gap: 6px;
    padding: 0 8px;
    width: calc(100% - 16px);
  }
  
  .option-text {
    font-size: 0.95rem;
    line-height: 1.3;
    margin-bottom: 6px;
    padding: 0 4px;
    word-break: break-word;
    hyphens: auto;
    max-width: 100%;
    overflow-wrap: break-word;
  }
  
  .score {
    font-size: 0.8rem;
  }
  
  .paris-result {
    font-size: 1.3rem;
    line-height: 1.3;
    padding: 0 12px;
  }
  
  .snob, .râleur, .touriste {
    font-size: 1.1rem;
    line-height: 1.3;
    padding: 0 12px;
  }
  
  .next-btn, .action-btn {
    font-size: 0.9rem;
    min-height: 48px;
    margin: 0 8px;
  }
}

/* Pour les très petits écrans */
@media (max-width: 360px) {
  .paris-title {
    font-size: 1.7rem;
    padding: 0 12px;
  }
  
  .paris-question {
    font-size: 1.1rem;
    padding: 0 12px;
  }
  
  .option-btn {
    min-height: 110px;
    padding: 10px 4px;
    margin: 0 1px;
  }
  
  .option-text {
    font-size: 0.85rem;
    padding: 0 6px;
  }
  
  .score {
    font-size: 0.75rem;
  }
  
  .paris-option {
    padding: 0 6px;
    width: calc(100% - 12px);
  }
}

/* Pour les écrans en mode paysage mobile */
@media (max-width: 768px) and (orientation: landscape) {
  .paris-bg {
    padding: 8px 6px;
  }
  
  .option-btn {
    min-height: 80px;
    padding: 8px 4px;
  }
  
  .option-text {
    font-size: 0.85rem;
  }
  
  .paris-option {
    padding: 0 6px;
  }
}

/* Correction spécifique pour éviter le débordement */
.v-btn__content {
  width: 100%;
  max-width: 100%;
}

:deep(.v-btn__content) {
  width: 100%;
  max-width: 100%;
}
</style>