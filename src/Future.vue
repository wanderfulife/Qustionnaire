<template>
  <div>
    <!-- Surveyor Name Input -->
     <div v-if="currentQuestionIndex === -1 && !isSurveyComplete">
      <form @submit.prevent="startSurvey">
        <label for="surveyorName">Nom de l'enquêteur:</label>
        <input type="text" id="surveyorName" v-model="surveyorName">
        <button type="submit">Commencer le Questionnaire</button>
      </form>
    </div>

    <!-- Questions Section -->
    <div v-if="currentQuestionIndex >= 0 && currentQuestionIndex < questions.length && !isSurveyComplete">
      <div>{{ questions[currentQuestionIndex].text }}</div>
      <select v-model="selectedOption">
        <option disabled value="">-- select an option --</option>
        <option v-for="(option, index) in questions[currentQuestionIndex].options" 
                :key="index" 
                :value="index">
          {{ option }}
        </option>
      </select>
      <button @click="submitResponse">Suivant</button>
      <button v-if="currentQuestionIndex > 0" @click="prevQuestion">Retour</button>
    </div>

    <!-- Responses Summary Table -->
    <div v-if="isSurveyComplete">
      <div>Questionnaire terminé. Réponses:</div>
      <button @click="downloadAsCSV">Télécharger Excel</button>
      <button @click="startNewSet">Démarrer nouveau questionnaire</button>
    </div>
  </div>
</template>


<script setup>
import { ref } from 'vue';

// Questions array
const questions = ref([
  { text: 'Question 1', options: ['Option 1', 'Option 2', 'Option 3'] },
  { text: 'Question 2', options: ['Option 1', 'Option 2', 'Option 3'] },
  { text: 'Question 3', options: ['Option 1', 'Option 2', 'Option 3'] },
  { text: 'Question 4', options: ['Option 1', 'Option 2', 'Option 3','Option 4', 'Option 5', 'Option 6'] },

  // Add more questions as needed
]);


const surveyorName = ref('');
// Current question index
const currentQuestionIndex = ref(-1);

// Array to store responses for the current set
const responses = ref([]);

// Array to store all sets of responses along with their start and finish times
const allResponses = ref([]);

// Selected option
const selectedOption = ref(null);

// Start and Finish times for each set
const startTime = ref(null);
const finishTime = ref(null);

const isSurveyComplete = ref(false); // New state variable

const generateUUID = () => {
  const s4 = () => Math.floor((1 + Math.random()) * 0x10000).toString(16).substring(1);
  return `${s4() + s4()}-${s4()}-${s4()}-${s4()}-${s4() + s4() + s4()}`;
};


const startSurvey = () => {
  if (surveyorName.value) {
    currentQuestionIndex.value = 0;
    startTime.value = new Date();
    isSurveyComplete.value = false; // Reset survey completion state
  } else {
    alert('Veuillez entrer le nom de l’enquêteur.');
  }
};

// Submit response and move to next question
const submitResponse = () => {
  if (selectedOption.value !== null) {
    responses.value[currentQuestionIndex.value] = selectedOption.value;
    if (currentQuestionIndex.value === questions.value.length - 1) {
      finishTime.value = new Date();
      allResponses.value.push({
        id: generateUUID(),
        surveyor: surveyorName.value,
        startTime: startTime.value,
        responses: [...responses.value],
        finishTime: finishTime.value
      });
      responses.value = [];
      isSurveyComplete.value = true; // Set survey completion state
      selectedOption.value = null;
    } else {
      currentQuestionIndex.value++;
      selectedOption.value = null;
    }
  }
};

const startNewSet = () => {
  currentQuestionIndex.value = -1;
  startTime.value = null;
  finishTime.value = null;
  responses.value = [];
  surveyorName.value = '';
  isSurveyComplete.value = false; // Reset survey completion state
};

const prevQuestion = () => {
  if (currentQuestionIndex.value > 0) {
    currentQuestionIndex.value--;
  }
};

const downloadAsCSV = () => {
  let csvContent = 'data:text/csv;charset=utf-8,';
  csvContent += 'Ordre,ID,Nom de l’enquêteur,Date,Heure Début,';
  questions.value.forEach((question, index) => {
    csvContent += `"${question.text}"${index < questions.value.length - 1 ? ',' : ''}`;
  });
  csvContent += ',Heure Fin\r\n';

  const formatDate = (date) => {
    if (!date) return '';
    const d = new Date(date);
    return `${d.getFullYear()}-${(d.getMonth() + 1).toString().padStart(2, '0')}-${d.getDate().toString().padStart(2, '0')}`;
  };

  const formatTime = (date) => {
    if (!date) return '';
    const d = new Date(date);
    return `${d.getHours().toString().padStart(2, '0')}:${d.getMinutes().toString().padStart(2, '0')}:${d.getSeconds().toString().padStart(2, '0')}`;
  };

        allResponses.value.forEach((set, setIndex) => {
    csvContent += `${setIndex + 1},"${set.id}","${set.surveyor}","${formatDate(set.startTime)}","${formatTime(set.startTime)}",`;
    set.responses.forEach((response, qIndex) => {
      csvContent += `"${questions.value[qIndex].options[response]}"${qIndex < questions.value.length - 1 ? ',' : ''}`;
    });
    csvContent += `,"${formatTime(set.finishTime)}"\r\n`;
        });
  
  const encodedUri = encodeURI(csvContent);
  const link = document.createElement('a');
  link.setAttribute('href', encodedUri);
  link.setAttribute('download', 'ODCaen.csv');
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
};
</script>


<style>
* {
  margin: 0;
  font-family:  Arial;
  background-color: #343541;
  font-size: 1.1em;
  color: beige;
  font-weight: bold;
}



</style>
