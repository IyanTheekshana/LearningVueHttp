<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <div v-if="isLoading" class="spinner">
        <span class="loader"></span>
      </div>
      <div v-if="errMessage" class="spinner">
        <p>{{ errMessage }}</p>
      </div>
      <div v-if="results.length === 0 && !errMessage" class="spinner">
        <p>No stored data to show</p>
      </div>

      <ul v-if="!isLoading && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from "./SurveyResult.vue";

export default {
  // props: ['results'],
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      errMessage: null,
    };
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.errMessage = null;
      fetch(
        "https://vue-http-demo-4c5ef-default-rtdb.europe-west1.firebasedatabase.app/surveys.json"
      )
        .then((response) => {
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          this.isLoading = false;
          const resultsArray = [];
          for (const id in data) {
            resultsArray.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }

          this.results = resultsArray;
        })
        .catch((error) => {
          console.error(error);
          this.isLoading = false;
          this.errMessage = "Failed to fetch data";
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

.spinner {
  padding: 2rem;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.loader {
  width: 48px;
  height: 48px;
  border: 5px solid #fff;
  border-bottom-color: #ff3d00;
  border-radius: 50%;
  display: inline-block;
  box-sizing: border-box;
  animation: rotation 1s linear infinite;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
