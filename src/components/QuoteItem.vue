<script setup>
defineProps({
  quoteText: String,
  quoteAuthor: String
})
</script>

<template>
  <li>
    <blockquote>
      <!-- As in some quotes all words are capitalised -->
      <p>"{{ quoteText.toLowerCase() }}"</p>
    </blockquote>
    <p>{{ quoteAuthor }}</p>
    <img src="../assets/icons/divider.svg" alt="divider" />  
  </li>
</template>

<style scoped></style>