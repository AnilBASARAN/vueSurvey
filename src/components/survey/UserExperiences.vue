<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">
          Load Submitted Experiences
        </base-button>
      </div>
      <p v-if="isLoading">Loading…</p>

      <ul v-else-if="!isLoading && results && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-else-if="!error && results.length == 0">
        No stored experiences found.Start adding !
      </p>
      <p v-else>{{ error }}</p>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch(
        'https://vue-http-demo-fa677-default-rtdb.firebaseio.com/surveys.json',
      )
        .then((response) => {
          if (!response.ok) {
            // Throw on any non-2xx status
            throw new Error(
              `Fetch failed (${response.status}): ${response.statusText}`,
            );
          }
          return response.json();
        })
        .then((data) => {
          // Immediately process data (no delay)
          this.isLoading = false;

          const results = [];
          for (const id in data) {
            if (Object.prototype.hasOwnProperty.call(data, id)) {
              results.push({
                id: id,
                name: data[id].name,
                rating: data[id].rating,
              });
            }
          }
          this.results = results;
        })
        .catch((error) => {
          this.isLoading = false;
          console.log(error);
          this.error = 'LoadExperiences failed!';
        });
    },
  },
  mounted() {
    this.loadExperiences();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
