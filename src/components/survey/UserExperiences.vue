<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <!-- <div>
        <base-button>Load Submitted Experiences</base-button>
      </div> -->
      <p v-if="isLoading">Loading...</p>
      <p v-else-if='!isLoading && error'>
        {{error}}
      </p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found. Start adding some survey results first
      </p>
      <ul v-else>
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
import SurveyResult from './SurveyResult.vue';

export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  components: {
    SurveyResult,
  },
  data() {
    return {
      isLoading: false,
      error: null,
    };
  },
  computed: {
    results() {
      return this.modelValue;
    },
    checkForHavingInfo() {
      return !this.isLoading && this.results?.length > 0;
    },
  },
  async mounted() {
    try {
      this.isLoading = true;
      this.error = null;
      const respons = await fetch(
        'https://vue-http-demo-kick-default-rtdb.firebaseio.com/surveys.json'
      );
      if (respons.ok) {
        this.isLoading = false;
        const data = await respons.json();
        this.$emit('update:modelValue', Object.values(data));
      }
    } catch (error) {
      this.isLoading = false;
      this.error = 'Failed to fetch data - please try again later!'
    }
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