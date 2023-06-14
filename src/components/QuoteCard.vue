<script setup>
    defineProps({
        quoteText: String,
        quoteAuthor: String
    })
</script>

<template>
  <figure class="card">  
    <figcaption>{{ quoteAuthor }}</figcaption>
    <blockquote>
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <button type="button" @click="$emit('onClickDoThis')">New quote</button>
  </figure>
</template>

<style scoped></style>
