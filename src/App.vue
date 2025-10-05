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
                    
                    <!-- SECTION DES OPTIONS CORRIGÉE -->
                    <div class="options-container mt-4">
                      <button
                        v-for="(option, index) in currentQuestion.options"
                        :key="index"
                        :class="['option-btn', selectedAnswer === index ? 'option-selected' : 'option-default']"
                        @click="selectAnswer(index)"
                      >
                        <div class="option-content">
                          <span class="option-text">{{ option.text }}</span>
                          <span class="option-score">({{ option.score }} pts)</span>
                        </div>
                      </button>
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
                    
                    <!-- BOUTON SUIVANT NATIF -->
                    <button
                      class="next-btn-native"
                      :disabled="selectedAnswer === null"
                      @click="nextQuestion"
                    >
                      <span>→</span>
                      Suivant
                    </button>
                  </v-sheet>
                </div>
                
                <div v-else key="result">
                  <v-sheet elevation="2" class="result-sheet pa-4" rounded="lg">
                    <h2 class="paris-result">Ton score : {{ score }} / {{ maxScore }}</h2>
                    <p v-if="score >= 36" class="snob">
                      <span class="icon">👑</span>
                      Snob du Marais : tu juges même les croissants !
                    </p>
                    <p v-else-if="score >= 18" class="râleur">
                      <span class="icon">☕</span>
                      Râleur Sympa : un Parisien en devenir.
                    </p>
                    <p v-else class="touriste">
                      <span class="icon">🗺️</span>
                      Touriste Perdu : Paris te snobe encore !
                    </p>
                    
                    <!-- BOUTONS D'ACTION NATIFS -->
                    <div class="action-buttons-container mt-4">
                      <button class="action-btn-native reset-btn" @click="resetQuiz">
                        <span>🔄</span>
                        Recommencer
                      </button>
                      <a class="action-btn-native share-btn" :href="shareUrl" target="_blank">
                        <span>🐦</span>
                        Partager sur X
                      </a>
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
  padding: 16px 8px;
  background-color: #1E3A8A;
}

.paris-sheet {
  background: rgba(255, 253, 208, 0.95);
  border-radius: 16px;
  overflow: hidden;
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

/* STYLES DES OPTIONS - VERSION SIMPLIFIÉE */
.options-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
  width: 100%;
}

.option-btn {
  width: 100%;
  min-height: 80px;
  padding: 12px 8px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-family: 'Lora', serif;
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

.option-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 0 4px;
}

.option-text {
  font-family: 'Lora', serif;
  font-size: 1rem;
  line-height: 1.3;
  text-align: center;
  margin-bottom: 4px;
  width: 100%;
  word-break: break-word;
  overflow-wrap: break-word;
  hyphens: auto;
}

.option-score {
  color: #8B0000;
  font-family: 'Lora', serif;
  font-size: 0.9rem;
  text-align: center;
}

/* Reste du CSS inchangé */
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

.snob, .râleur, .touriste {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-family: 'Lora', serif;
  font-weight: bold;
  font-size: 1.4rem;
  text-align: center;
  margin: 16px 0;
}

.snob {
  color: #8B0000;
}

.râleur {
  color: #1E3A8A;
}

.touriste {
  color: #4B5563;
}

.icon {
  font-size: 1.6rem;
}

/* BOUTONS NATIFS POUR LE CENTRAGE PARFAIT */
.next-btn-native, .action-btn-native {
  width: 100%;
  min-height: 48px;
  padding: 12px 24px;
  border: none;
  border-radius: 24px;
  font-family: 'Lora', serif;
  font-size: 1rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s ease;
  text-decoration: none;
  margin-top: 16px;
}

.next-btn-native {
  background-color: #b71c1c;
  color: white;
}

.next-btn-native:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
  opacity: 0.6;
}

.action-buttons-container {
  display: flex;
  gap: 12px;
  width: 100%;
}

.action-btn-native {
  flex: 1;
  min-height: 48px;
}

.reset-btn {
  background-color: #b71c1c;
  color: white;
}

.share-btn {
  background-color: transparent;
  color: #0d47a1;
  border: 2px solid #0d47a1;
}

.next-btn-native:hover:not(:disabled),
.reset-btn:hover {
  background-color: #8B0000;
  transform: translateY(-2px);
}

.share-btn:hover {
  background-color: #0d47a1;
  color: white;
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

/* CORRECTIONS MOBILE RADICALES */
@media (max-width: 768px) {
  .paris-bg {
    padding: 8px 4px;
  }
  
  .paris-sheet {
    margin: 0;
    padding: 12px 8px !important;
  }
  
  .paris-title {
    font-size: 1.7rem;
    padding: 0 8px;
  }
  
  .paris-subtitle {
    font-size: 0.95rem;
    padding: 0 8px;
  }
  
  .paris-question {
    font-size: 1.1rem;
    padding: 0 8px;
    margin-bottom: 16px;
  }
  
  .options-container {
    gap: 8px;
    width: 100%;
  }
  
  .option-btn {
    min-height: 85px;
    padding: 10px 6px;
    margin: 0;
    width: 100%;
  }
  
  .option-content {
    padding: 0 2px;
    width: 100%;
  }
  
  .option-text {
    font-size: 0.88rem;
    line-height: 1.2;
    padding: 0 2px;
  }
  
  .option-score {
    font-size: 0.75rem;
  }
  
  .next-btn-native, .action-btn-native {
    min-height: 44px;
    font-size: 0.9rem;
    gap: 6px;
  }
  
  .action-buttons-container {
    flex-direction: column;
    gap: 8px;
  }
  
  .snob, .râleur, .touriste {
    font-size: 1.1rem;
    gap: 6px;
  }
  
  .icon {
    font-size: 1.4rem;
  }
}

/* Pour les très petits écrans */
@media (max-width: 360px) {
  .paris-bg {
    padding: 6px 2px;
  }
  
  .paris-sheet {
    padding: 10px 6px !important;
  }
  
  .paris-title {
    font-size: 1.5rem;
  }
  
  .paris-question {
    font-size: 1rem;
  }
  
  .option-btn {
    min-height: 90px;
    padding: 8px 4px;
  }
  
  .option-text {
    font-size: 0.82rem;
  }
  
  .next-btn-native, .action-btn-native {
    min-height: 42px;
    font-size: 0.85rem;
    gap: 4px;
  }
}
</style>