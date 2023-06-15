<script setup>
    defineProps({
        quoteText: "String"
    })
</script>

<template>
  <article>
    <blockquote>
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <img src="../assets/icons/divider.svg" alt="divider" />  
  </article>
</template>

<style scoped></style>