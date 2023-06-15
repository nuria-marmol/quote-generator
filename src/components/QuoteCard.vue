<script setup>
    defineProps({
        quoteText: String,
        quoteAuthor: String
    })
</script>

<template>
  <figure class="card">  
    <figcaption @click="$emit('clickFigcaption')">
      {{ quoteAuthor }}
    </figcaption>
    <blockquote>
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <button 
      type="button" 
      @click="$emit('clickButton')"
    >New quote</button>
  </figure>
</template>

<style scoped></style>
