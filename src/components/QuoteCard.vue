<script setup>
    defineProps({
        quoteText: String,
        quoteAuthor: String
    })
</script>

<template>
  <div class="card">  
    <p>{{ quoteAuthor }}</p>
    <p>"{{ quoteText }}"</p>
    <button type="button" @click="$emit('onClickDoThis')">New quote</button>
  </div>
</template>

<style scoped></style>
