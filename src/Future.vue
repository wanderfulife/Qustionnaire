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
      <button @click="submitResponse">Next</button>
      <button v-if="currentQuestionIndex > 0" @click="prevQuestion">Return</button>

    </div>

    <!-- Responses Summary Table -->
    <div v-else>
      <div>All questions answered for this set. See cumulative responses below:</div>
      <table>
        <tr>
          <th>Set</th>
          <th>Question</th>
          <th>Response</th>
        </tr>
        <tr v-for="(set, setIndex) in allResponses" :key="setIndex">
          <td rowspan="{{ questions.length }}">{{ setIndex + 1 }}</td>
          <td v-for="(response, index) in set" :key="index">
            <template v-if="index === 0">
              <td>{{ questions[index].text }}</td>
              <td>{{ questions[index].options[response] }}</td>
            </template>
            <template v-else>
              <tr>
                <td>{{ questions[index].text }}</td>
                <td>{{ questions[index].options[response] }}</td>
              </tr>
            </template>
          </td>
        </tr>
      </table>
	  <button @click="downloadAsCSV">Download as CSV</button>
      <button @click="startNewSet">Start New Set of Questions</button>
    </div>
  </div>
</template>
<script setup>
import { ref } from 'vue';

// Questions array
const questions = ref([
  { text: 'Question 1', options: ['Option 1', 'Option 2', 'Option 3'] },
  { text: 'Question 2', options: ['Option 1', 'Option 2', 'Option 3'] },
  // Add more questions as needed
]);

// Current question index
const currentQuestionIndex = ref(0);

// Array to store responses for the current set
const responses = ref([]);

// Array to store all sets of responses
const allResponses = ref([]);

// Selected option
const selectedOption = ref(null);

// Submit response and move to next question
const submitResponse = () => {
  if (selectedOption.value !== null) {
    responses.value[currentQuestionIndex.value] = selectedOption.value;
    if (currentQuestionIndex.value === questions.value.length - 1) {
      allResponses.value.push([...responses.value]); // Add responses to allResponses
      responses.value = []; // Reset responses for next set
    }
    currentQuestionIndex.value++;
    selectedOption.value = null; // Reset for next question
  }
};

// Start a new set of questions
const startNewSet = () => {
  currentQuestionIndex.value = 0;
};

// Navigate back to the previous question
const prevQuestion = () => {
  if (currentQuestionIndex.value > 0) {
    currentQuestionIndex.value--;
  }
};

const downloadAsCSV = () => {
  let csvContent = 'data:text/csv;charset=utf-8,';

  // Add header row with question texts
  csvContent += 'Set,';
  questions.value.forEach((question, index) => {
    csvContent += `"${question.text}"${index < questions.value.length - 1 ? ',' : ''}`;
  });
  csvContent += '\r\n';

  // Add rows for each response set
  allResponses.value.forEach((set, setIndex) => {
    csvContent += `Set ${setIndex + 1},`;
    questions.value.forEach((_, qIndex) => {
      csvContent += `"${questions.value[qIndex].options[set[qIndex]]}"${qIndex < questions.value.length - 1 ? ',' : ''}`;
    });
    csvContent += '\r\n';
  });

  const encodedUri = encodeURI(csvContent);
  const link = document.createElement('a');
  link.setAttribute('href', encodedUri);
  link.setAttribute('download', 'responses.csv');
  document.body.appendChild(link); // Required for Firefox
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

input {
  border: 0;
  outline: 0;
	color: grey;
}
.container {
  display: flex;
}

</style>
