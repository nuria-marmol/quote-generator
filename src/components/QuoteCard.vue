<script setup>
    defineProps({
        quoteText: String,
        quoteAuthor: String
    })
</script>

<template>
  <div>
    <p>"{{ quoteText }}"</p>
    <p>{{ quoteAuthor }}</p>
    <button type="button" @click="$emit('onClickDoThis')">New quote</button>
  </div>
</template>

<style scoped></style>
