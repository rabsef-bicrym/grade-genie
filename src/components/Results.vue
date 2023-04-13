<template>
  <div class="min-h-screen py-12 px-4 relative bg-gradient-to-l from-blue-300 via-blue-400 to-blue-600">
    <div class="flex flex-col items-center justify-center h-screen">
      <div class="bg-white p-8 rounded-lg shadow-md w-full sm:w-2/3 md:w-1/2 lg:w-1/3">
        <h2>Generated Report Card Comments:</h2>

        <h3>Student Information</h3>
        <p>Student Name: {{ parsedResults.student }}</p>
        <p>Grade: {{ parsedResults.grade }}</p>

        <h3>Report Card Comments</h3>
        <p>{{ parsedResults.comment }}</p>

        <button @click="$emit('startOver')" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Start Over</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["results"],
  computed: {
    parsedResults() {
      let data = JSON.parse(this.results);

      return {
        student: `${data.student}`,
        grade: `${data.grade}`,
        comment: `${data.comment}`,
      };
    },
  },
};
</script>