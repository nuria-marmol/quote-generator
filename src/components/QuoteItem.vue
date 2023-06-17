<script setup>
    defineProps({
        quoteText: "String"
    })
</script>

<template>
  <li>
    <blockquote>
      <p>"{{ quoteText }}"</p>
    </blockquote>
    <img src="../assets/icons/divider.svg" alt="divider" />  
  </li>
</template>

<style scoped></style>