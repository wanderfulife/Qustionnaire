<template>
  <div>
    <!-- Questions Section -->
    <div v-if="currentQuestionIndex < questions.length">
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
    <div v-else>
      <div>Questionnaire terminé. réponses:</div>
      <button @click="downloadAsCSV">Telecharger Excel</button>
      <button @click="startNewSet">Demarrer nouveau Questionnaire</button>
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

// Current question index
const currentQuestionIndex = ref(0);

// Array to store responses for the current set
const responses = ref([]);

// Array to store all sets of responses along with their start and finish times
const allResponses = ref([]);

// Selected option
const selectedOption = ref(null);

// Start and Finish times for each set
const startTime = ref(null);
const finishTime = ref(null);

// Submit response and move to next question
const submitResponse = () => {
  if (selectedOption.value !== null) {
    // Record start time at the first response
    if (currentQuestionIndex.value === 0 && !startTime.value) {
      startTime.value = new Date();
    }

    responses.value[currentQuestionIndex.value] = selectedOption.value;

    if (currentQuestionIndex.value === questions.value.length - 1) {
      finishTime.value = new Date(); // Record finish time
      allResponses.value.push({
        responses: [...responses.value],
        startTime: startTime.value,
        finishTime: finishTime.value
      });
      responses.value = []; // Reset responses for next set
      startTime.value = null; // Reset start time for next set
    }

    currentQuestionIndex.value++;
    selectedOption.value = null; // Reset for next question
  }
};

// Start a new set of questions
const startNewSet = () => {
  currentQuestionIndex.value = 0;
  startTime.value = null; // Reset start time
  finishTime.value = null; // Reset finish time
};

// Navigate back to the previous question
const prevQuestion = () => {
  if (currentQuestionIndex.value > 0) {
    currentQuestionIndex.value--;
  }
};
const downloadAsCSV = () => {
  let csvContent = 'data:text/csv;charset=utf-8,';
  csvContent += 'Ordre,Date,Heure Début,';
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
    csvContent += `${setIndex + 1},"${formatDate(set.startTime)}","${formatTime(set.startTime)}",`;
    set.responses.forEach((response, qIndex) => {
      csvContent += `"${questions.value[qIndex].options[response]}"${qIndex < questions.value.length - 1 ? ',' : ''}`;
    });
    csvContent += `,"${formatTime(set.finishTime)}"\r\n`;
  });

  const encodedUri = encodeURI(csvContent);
  const link = document.createElement('a');
  link.setAttribute('href', encodedUri);
  link.setAttribute('download', 'responses.csv');
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
