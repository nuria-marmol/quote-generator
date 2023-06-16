<script setup>
  import QuoteCard from './components/QuoteCard.vue'
  import QuoteArticle from './components/QuoteArticle.vue'

  import { ref, onMounted, computed } from 'vue'

  const randomQuote = ref({})
  const authorQuotes = ref([]) // for other quotes from that author
  const noDuplicatedQuotes = ref({})
  const seeRandom = ref(true) // for showing/hiding card
  const cardLoadingMessage = ref("")

  /**
  * Generating random loading message for the card
  */
  function getLoadingMessage() {
    const messages = ["Wait for it...", "Just a tick...", "Wait a moment...", "Let me think...", "You'll love this one..."]
    const ultimateMessage = messages[Math.floor(Math.random() * messages.length)]
    cardLoadingMessage.value = ultimateMessage
  }

  /**
  * Getting a random quote for the card
  */
  function getRandomQuote() {
    getLoadingMessage()
    // Clearing previous random quote
    randomQuote.value = {}
    // Showing card again when clicking button below several quotes
    seeRandom.value = true
    // Clearing several previous quotes from author
    authorQuotes.value = []
    noDuplicatedQuotes.value = {}
    // API docs: https://pprathameshmore.github.io/QuoteGarden/
    fetch("https://quote-garden.onrender.com/api/v3/quotes/random")
      .then((response) => response.json())
      .then((json) => (randomQuote.value = json.data[0]))
  }

  /**
  * Getting other quotes from author
  */
  function getAuthorQuotes() {
    // Hiding card with random quote
    seeRandom.value = false
    const authorLower = randomQuote.value.quoteAuthor.toLowerCase()
    // Replacing all spaces for %20
    const authorSlug = authorLower.replace(/ /g, '%20')
    fetch(`https://quote-garden.onrender.com/api/v3/quotes?author=${authorSlug}&limit=12`)
      .then((response) => response.json())
      .then((json) => {
        authorQuotes.value = json.data
        // Avoiding duplicated quotes
        noDuplicatedQuotes.value = new Set()
        authorQuotes.value.forEach(el => {
          noDuplicatedQuotes.value.add(el.quoteText)
        })
        return noDuplicatedQuotes.value
      })
  }

  // Returns true if there is a random quote
  const isThereRandomQuote = computed( () => {
    return Object.keys(randomQuote.value).length > 0
  })

  // Returns true if there are quotes from a concrete author
  const areThereSeveralQuotes = computed( () => {
    return authorQuotes.value.length > 0
  })

  onMounted(async () => {
    await getRandomQuote()    
  })
</script>

<template>
  <!-- Random quote -->
  <QuoteCard
    v-if="seeRandom"
    :showLoading="!isThereRandomQuote"
    :loadingMessage="cardLoadingMessage"
    :quoteText="randomQuote.quoteText"
    :quoteAuthor="randomQuote.quoteAuthor"
    @clickFigcaption="getAuthorQuotes"
    @clickButton="getRandomQuote"
  />

  <!-- Several quotes from concrete author -->
  <section v-if="!seeRandom" class="author container">
    <p>{{ randomQuote.quoteAuthor }}</p>
    <p v-if="!areThereSeveralQuotes">Loading quotes...</p>
    <QuoteArticle      
      v-for="quote in noDuplicatedQuotes"
      :quoteText="quote"
    />
    <button      
      type="button" 
      @click="getRandomQuote"
    >New quote</button>
  </section>
</template>

<style scoped></style>
