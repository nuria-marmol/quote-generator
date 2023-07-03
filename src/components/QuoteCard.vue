<script setup>
    defineProps({
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
  <figure class="card">
    <figcaption v-if="!showLoading" @click="$emit('clickFigcaption')">
      {{ quoteAuthor }} &rarr;
    </figcaption>
    <p v-if="showLoading">{{ loadingMessage }}</p>
    <blockquote v-if="!showLoading">
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <button
      type="button"
      class="button"
      @click="$emit('clickButton')"
    >New quote</button>
  </figure>
</template>

<style scoped></style>
