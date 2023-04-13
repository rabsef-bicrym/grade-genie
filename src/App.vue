<template>
  <div>
    <!-- Loading Modal -->
    <div v-if="loading" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white p-4 rounded text-center">
        <p>{{ loadingMessage }}</p>
        <template v-if="errorMessage">
          <button @click="submitData" class="mt-2 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
            Try Again
          </button>
        </template>
      </div>
    </div>
    <!-- First time user is prompted to enter an OpenAI API key -->
    <div v-if="!apiKey">
      <label for="apiKeyInput">Enter your OpenAI API Key:</label>
      <input type="password" id="apiKeyInput" v-model="inputApiKey" />
      <button @click="saveApiKey">Save API Key</button>
    </div>

    <!-- Data Entry -->
    <div v-else>
      <DataEntry v-if="!results" @submitData="submitData" />
      <Results
        v-else
        :results="results"
        @startOver="startOver"
      />
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import axios from "axios";
import DataEntry from "./components/DataEntry.vue";
import Results from "./components/Results.vue";

export default {
  components: {
    DataEntry,
    Results,
  },
  data() {
    return {
      apiKey: localStorage.getItem("apiKey"),
      inputApiKey: "",
      results: null,
      loading: false,
      errorMessage: "",
    };
  },
  computed: {
    loadingMessage() {
      return this.errorMessage ? this.errorMessage : "Loading, please wait...";
    }
  },
  methods: {
    saveApiKey() {
      localStorage.setItem("apiKey", this.inputApiKey);
      this.apiKey = this.inputApiKey;
    },
    async submitData(data) {
      console.log(data);
      this.errorMessage = "";
      this.loading = true;
      const subjects = JSON.stringify(data.subjects.map(subject => {
        return {
          name: subject.name,
          "dev-lev": subject.level,
          "descriptors": subject.descriptors
        }
      }))
      console.log(subjects);
      const payload = {
        model: "gpt-4",
        temperature: 0.8,
        messages: [
          {
            "role": "system",
            "content": `
            You are Grade Genie. You are a piece of middleware used in the Grade Genie website.
            The Grade Genie website allows teachers to submit short descriptions of a child's progress in
            (a) social emotional development and (b) the various subjects the teacher taught the student.

            The website will send you prompts in the following JSON format:

            {
              "student": {
                "first": "Student's First Name",
                "last": "Student's Last Name",
                "grade": "1-12 indicating the primary American grade levels"
              },
              "soc-emo": {
                "dev-lev": "Development Level repersented as <1> Beginning, <2> Developing, <3> Secure, <4> Advanced",
                "descriptors": [
                  "Short descriptions of the student's social emotional development observed by the teacher this year"
                ]
              },
              "subjects": [
                {
                  "name": "Subject Name",
                  "dev-lev": "Development Level represented as <1> Beginning, <2> Developing, <3> Secure, <4> Advanced",
                  "descriptors": [
                    "Short descriptions of the student's development in the given subject, observed by the teacher, this year"
                  ]
                }
              ]
            }

            Your response MUST be in the following JSON format:

            {
              "student": "<Student's First and Last name>",
              "grade": "<Grade name (e.g. Fourth Grade, or Sophomore)>",
              "comment": "<Report card comment>"
            }

            <Report card comment> will be a full page report on the student's progress, and will include the following:
              1. One paragraph introducing the report and identifying the student, grade, and subjects covered in the report.
                 These should flow directly from the input from the website you receive.
              2. One paragraph per subject in the "subjects" array from the input, describing the student's progress and grade
                 and highlighting the descriptors provided in that subject's "descriptors" array. Reframe negative comments in
                 a constructive, professional light but do not change their substantive content.
              3. One paragraph describing the student's social-emotional development.
              4. One concluding paragraph with a synthesis of the student's progress across all graded areas from that instructor.
            
            The <Report card comment> must abide by the following rules:
              1. Each paragraph should be separated by a newline character.
              2. UNDER NO CIRCUMSTANCES should you write about subjects NOT covered in the input you received from the website.
              3. DO NOT INVENT EVENTS OUTSIDE OF THAT PROVIDED TO YOU.
              4. Write the narrative from the perspective of the teacher, as if it is the teacher writing.

            You will now receive input from the Grade Genie website:
            `
          },
          {
            "role": "user",
            "content": `
            {
              "student": {
                "first": "${data.studentFirstName}",
                "last": "${data.studentLastName}",
                "grade": "${data.studentGrade}"
              },
              "soc-emo": {
                "dev-lev": "${data.socialEmotionalDevelopment.level}",
                "descriptors": ${data.socialEmotionalDevelopment.descriptors}
              },
              "subjects": ${subjects}
            }
            `
          }
        ]
      };
      try {
        const response = await axios.post(
          "https://api.openai.com/v1/chat/completions",
          payload,
          {
            headers: {
              "Content-Type": "application/json",
              Authorization: `Bearer ${this.apiKey}`,
            },
          }
        );
        console.log(response.data);
        console.log(response.data.choices[0].message.content);
        this.results = response.data.choices[0].message.content;
      } catch (error) {
        console.error("Error submitting data:", error);
        this.errorMessage = "An error occurred. Please try again."
      } finally {
        this.loading = false;
      }
    },
    startOver() {
      this.results = null;
    },
  },
};
</script>