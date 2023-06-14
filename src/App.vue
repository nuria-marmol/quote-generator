<script setup>
  import QuoteCard from './components/QuoteCard.vue'

  import { ref, onMounted } from 'vue'

  const quote = ref({})

  function getQuote() {
    // API docs: https://pprathameshmore.github.io/QuoteGarden/
    fetch("https://quote-garden.onrender.com/api/v3/quotes/random")
      .then((response) => response.json())
      .then((json) => (quote.value = json.data[0]))
  }

  onMounted(async () => {
    await getQuote()
  })
</script>

<template>
  <QuoteCard
    :quoteText="quote.quoteText"
    :quoteAuthor="quote.quoteAuthor"
    @onClickDoThis="getQuote"
  />
</template>

<style scoped></style>
