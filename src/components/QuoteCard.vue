<script setup>
    defineProps({
        showLoading: {
          type: Boolean,
          default: true
        },
        quoteText: String,
        quoteAuthor: String
    })
</script>

<template>
  <figure class="card">
    <figcaption v-if="!showLoading" @click="$emit('clickFigcaption')">
      {{ quoteAuthor }} ->
    </figcaption>
    <p v-if="showLoading">Wait for it...</p>
    <blockquote v-if="!showLoading">
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <button 
      type="button" 
      @click="$emit('clickButton')"
    >New quote</button>
  </figure>
</template>

<style scoped></style>
