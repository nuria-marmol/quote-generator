<script setup>
defineProps({
  cardClassCondition: Boolean,
  showLoading: {
    type: Boolean,
    default: true
  },
  loadingMessage: String,
  quoteText: String,
  quoteAuthor: String
})
</script>

<template>
  <!-- The card will have a second class if the quote is too long -->
  <figure
    class="card" 
    :class="{ 'card--bigger': cardClassCondition }"
  >
    <figcaption v-if="!showLoading">{{ quoteAuthor }}</figcaption>
    <p v-if="showLoading">{{ loadingMessage }}</p>
    <blockquote v-if="!showLoading">
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <button
      type="button"
      class="button"
      @click="$emit('clickButton')"
    >New quote</button>
    <button class="button--secondary" @click="$emit('clickSeeAll')">See full list &rarr;</button>
  </figure>
  
</template>

<style scoped></style>
