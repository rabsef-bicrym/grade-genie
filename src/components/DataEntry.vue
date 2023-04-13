<template>
  <div class="min-h-screen py-12 px-4 relative bg-gradient-to-l from-blue-300 via-blue-400 to-blue-600">
    <form class="mx-auto max-w-2xl bg-white p-8 rounded-lg shadow-lg" @submit.prevent="$emit('submitData', formData)">
      <h3 class="block text-lg bold font-large text-gray-800">Grade Genie</h3>
      <!-- Student Name -->
      <div class="grid grid-cols-2 gap-4 mb-4">
        <div>
          <label for="studentFirstName" class="block text-sm font-medium text-gray-700">First Name:</label>
          <input id="studentFirstName" v-model="formData.studentFirstName" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm" />
        </div>
        <div>
          <label for="studentLastName" class="block text-sm font-medium text-gray-700">Last Name:</label>
          <input id="studentLastName" v-model="formData.studentLastName" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm" />
        </div>
      </div>

      <!-- Student Grade -->
      <div class="mb-4">
        <label for="studentGrade" class="block text-sm font-medium text-gray-700">Grade:</label>
        <select id="studentGrade" v-model="formData.studentGrade" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm cursor-pointer">
          <option disabled value="">Select grade</option>
          <option v-for="grade in 12" :key="grade" :value="grade">{{ grade }}</option>
        </select>
      </div>

      <!-- Social Emotional Development -->
      <div class="mb-4">
        <label class="block text-sm font-medium text-gray-700">Social Emotional Development Level:</label>
        <select v-model="formData.socialEmotionalDevelopment.level" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm cursor-pointer">
          <option disabled value="">Select level</option>
          <option v-for="level in 4" :key="level" :value="level">{{ level }}</option>
        </select>
      </div>
      <div class="mb-4">
        <label class="block text-sm font-medium text-gray-700">Descriptors:</label>
        <div class="mt-1">
          <input v-model="newSocialEmotDescriptor" @keyup.enter="addSocialEmotDescriptor" class="border border-gray-300 rounded-md shadow-sm p-2 inline-block w-2/3" />
          <button @click.prevent="addSocialEmotDescriptor" class="inline-block bg-blue-500 text-white px-3 py-1 rounded-md shadow-sm hover:bg-blue-600 transition w-1/3">Add</button>
        </div>
        <ul class="list-disc pl-7 mt-2">
          <li v-for="(descriptor, index) in formData.socialEmotionalDevelopment.descriptors" :key="index">
            <span class="inline-block w-full">
              {{ descriptor }}
              <button @click="removeSocialEmotDescriptor(index)" class="float-right bg-red-500 text-white px-2 py-1 rounded-md shadow-sm hover:bg-red-600 transition">Remove</button>
            </span>
          </li>
        </ul>
      </div>

      <!-- Subjects -->
      <div>
        <h3 class="text-lg mb-4">Subjects</h3>
        <div v-for="(subject, index) in formData.subjects" :key="index">
          <h4 class="mb-2">Subject {{ index + 1 }}</h4>
          <div class="grid grid-cols-2 gap-4 mb-4">
            <label class="block text-sm font-medium text-gray-700">Subject Name:</label>
            <input v-model="subject.name" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm" />
            <label class="block text-sm font-medium text-gray-700">Development Level:</label>
            <select v-model="subject.level" required class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm cursor-pointer">
              <option disabled value="">Select level</option>
              <option v-for="level in 4" :key="level" :value="level">{{ level }}</option>
            </select>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700">Descriptors:</label>
            <div class="mt-1">
              <input v-model="newSubjectDescriptors[index]" @keyup.enter="addSubjectDescriptor(index)" class="border border-gray-300 rounded-md shadow-sm p-2 inline-block w-2/3" />
              <button @click.prevent="addSubjectDescriptor(index)" class="inline-block bg-blue-500 btn text-white px-3 py-1 rounded-md shadow-sm hover:bg-blue-600 transition w-1/3">Add</button>
            </div>
            <ul class="list-disc pl-7 mt-2">
              <li v-for="(descriptor, descriptorIndex) in subject.descriptors" :key="descriptorIndex">
                <span class="inline-block w-full">
                  {{ descriptor }}
                  <button @click="removeSubjectDescriptor(index, descriptorIndex)"
                    class="float-right bg-red-500 text-white px-2 py-1 rounded-md shadow-sm hover:bg-red-600 transition">Remove</button>
                </span>
              </li>
            </ul>
          </div>
          <button @click.prevent="removeSubject(index)" class="mb-2 text-white bg-red-500 px-3 py-1 rounded-md shadow-sm cursor-pointer hover:bg-red-600 transition">Remove Subject</button>
        </div>
        <button @click.prevent="addSubject" class="mb-4 text-white bg-blue-500 px-3 py-1 rounded-md shadow-sm cursor-pointer hover:bg-blue-600 transition">Add Subject</button>
      </div>

      <button type="submit" class="text-white bg-blue-500 w-full p-2 rounded-md shadow-sm cursor-pointer hover:bg-blue-600 transition">Submit</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        studentFirstName: "",
        studentLastName: "",
        studentGrade: "",
        socialEmotionalDevelopment: {
          level: "",
          descriptors: [],
        },
        subjects: [],
      },
      newSocialEmotDescriptor: "",
      newSubjectDescriptors: [],
    };
  },
  methods: {
    addSubject() {
      this.formData.subjects.push({ name: "", level: "", descriptors: [] });
      this.newSubjectDescriptors.push("");
    },
    removeSubject(index) {
      this.formData.subjects.splice(index, 1);
      this.newSubjectDescriptors.splice(index, 1);
    },
    addSocialEmotDescriptor() {
      if (this.newSocialEmotDescriptor) {
        this.formData.socialEmotionalDevelopment.descriptors.push(this.newSocialEmotDescriptor);
        this.newSocialEmotDescriptor = "";
      }
    },
    removeSocialEmotDescriptor(index) {
      this.formData.socialEmotionalDevelopment.descriptors.splice(index, 1);
    },
    addSubjectDescriptor(index) {
      const descriptor = this.newSubjectDescriptors[index];
      if (descriptor) {
        this.formData.subjects[index].descriptors.push(descriptor);
        this.newSubjectDescriptors.splice(index, 1, "");
      }
    },
    removeSubjectDescriptor(subjectIndex, descriptorIndex) {
      this.formData.subjects[subjectIndex].descriptors.splice(descriptorIndex, 1);
    },
  },
};
</script>