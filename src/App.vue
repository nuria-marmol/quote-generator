<script setup>
import QuoteCard from './components/QuoteCard.vue'
import QuoteItem from './components/QuoteItem.vue'

import { ref, computed, onBeforeMount, onMounted } from 'vue'

const randomQuote = ref({})
const allQuotes = ref([]) // for other quotes from that author
const currentPage = ref(1)
const totalPages = ref(1)
const quotesPerPage = ref(15);
const quotesToSkip = ref(0);
const totalQuotes = ref(1000);
const seeRandom = ref(true) // for showing/hiding card
const cardLoadingMessage = ref("")
const isMobile = ref(false)

/**
* Generating random loading message for the card
*/
function getLoadingMessage() {
  const messages = ["Wait for it...", "Just a tick...", "Wait a moment...", "Let me think...", "You'll love this one..."]
  const ultimateMessage = messages[Math.floor(Math.random() * messages.length)]
  cardLoadingMessage.value = ultimateMessage
}

/**
* Before generating a new random quote from the list of quotes
*/
function resetCurrentPage() {
  currentPage.value = 1
  getRandomQuote()
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
  allQuotes.value = []

  // API docs: https://dummyjson.com/docs/quotes
  fetch("https://dummyjson.com/quotes/random")
    .then((response) => response.json())
    .then((json) => (randomQuote.value = json))
}

function goToNextPage() {
  currentPage.value += 1
  quotesToSkip.value = quotesToSkip.value + quotesPerPage.value;
  getAllQuotes()
}

function goToPreviousPage() {
  currentPage.value -= 1
  quotesToSkip.value = quotesToSkip.value - quotesPerPage.value;
  getAllQuotes()
}

function getAllQuotes() {
  // Hiding card with random quote
  seeRandom.value = false
  // In case the pagination is used: for activating the loading again
  allQuotes.value = []

  fetch(`https://dummyjson.com/quotes?limit=${quotesPerPage.value}&skip=${quotesToSkip.value}`)
    .then((response) => response.json())
    .then((json) => {
      allQuotes.value = json.quotes;
      totalQuotes.value = json.total;
      totalPages.value = Math.floor(json.total / quotesPerPage.value);
    })
}

/**
*  Checking if a mobile device is being used
*/
function checkMobileDevice() {
  if (window.innerWidth <= 480) {
    isMobile.value = true
  } else {
    isMobile.value = false
  }
}

// Returns true if there is a random quote
const isThereRandomQuote = computed( () => {
  return Object.keys(randomQuote.value).length > 0
})

// Returns true if there are quotes from a concrete author
const areThereSeveralQuotes = computed( () => {
  return allQuotes.value.length > 0
})

// Returns true if the quote is too long
const biggerCard = computed(() => {
  return randomQuote.value.quoteText?.length > 200
})

onBeforeMount(() => {
  checkMobileDevice()
})

onMounted(async () => {
  await getRandomQuote()
  window.addEventListener('resize', checkMobileDevice)
})
</script>

<template>
  <main class="container">
    <!-- Random quote -->
    <QuoteCard
      v-if="seeRandom"
      :cardClassCondition="isMobile && biggerCard"
      :showLoading="!isThereRandomQuote"
      :loadingMessage="cardLoadingMessage"
      :quoteText="randomQuote.quote"
      :quoteAuthor="randomQuote.author"
      @clickSeeAll="getAllQuotes"
      @clickButton="getRandomQuote"
    />

    <!-- Full list of quotes -->
    <section v-if="!seeRandom" class="author">
      <p v-if="!areThereSeveralQuotes">Loading quotes...</p>
      <ul>
        <QuoteItem     
          v-for="(quote, index) in allQuotes"
          v-bind:key="index"
          :quoteText="quote.quote"
          :quoteAuthor="quote.author"
        />
      </ul>
      
      <!-- Pagination will be visible if there is more than 1 page and once the quotes have been loaded -->
      <div v-if="areThereSeveralQuotes && totalQuotes > quotesPerPage" class="author__pagination">
        <button
          v-if="currentPage > 1"
          type="button"
          @click="goToPreviousPage"
        >&larr;</button>
        <span>Page {{ currentPage }} of {{ totalPages }}</span>
        <button
          v-if="currentPage + 1 <= totalPages"
          type="button" 
          @click="goToNextPage"      
        >&rarr;</button>
      </div>
      <button      
        type="button"
        class="button"
        @click="resetCurrentPage"
      >New quote</button>
    </section>
  </main>
</template>

<style scoped></style>
