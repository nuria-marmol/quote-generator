<script setup>
  import QuoteCard from './components/QuoteCard.vue'
  import QuoteItem from './components/QuoteItem.vue'

  import { ref, computed, onBeforeMount, onMounted } from 'vue'

  const randomQuote = ref({})
  const authorQuotes = ref([]) // for other quotes from that author
  const authorPage = ref(1)
  const totalPages = ref(1)
  const noDuplicateQuotes = ref({})
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
  function resetAuthorPage() {
    authorPage.value = 1
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
    authorQuotes.value = []
    noDuplicateQuotes.value = {}
    // API docs: https://pprathameshmore.github.io/QuoteGarden/
    fetch("https://quote-garden.onrender.com/api/v3/quotes/random")
      .then((response) => response.json())
      .then((json) => (randomQuote.value = json.data[0]))
  }

  function goToNextPage() {
    authorPage.value += 1
    getAuthorQuotes()
  }

  function goToPreviousPage() {
    authorPage.value -= 1
    getAuthorQuotes()
  }

  /**
  * Getting other quotes from author
  */
  function getAuthorQuotes() {
    // Hiding card with random quote
    seeRandom.value = false
    // In case the pagination is used: emptying previous quotes
    noDuplicateQuotes.value = {}
    // In case the pagination is used: for activating the loading again
    authorQuotes.value = []
    const authorLower = randomQuote.value.quoteAuthor.toLowerCase()
    // Replacing all spaces for %20
    const authorSlug = authorLower.replace(/ /g, '%20')
    fetch(`https://quote-garden.onrender.com/api/v3/quotes?author=${authorSlug}&page=${authorPage.value}`)
      .then((response) => response.json())
      .then((json) => {
        totalPages.value = json.pagination.totalPages
        authorQuotes.value = json.data
        // Avoiding duplicate quotes
        noDuplicateQuotes.value = new Set()
        authorQuotes.value.forEach(el => {
          noDuplicateQuotes.value.add(el.quoteText)
        })
        return noDuplicateQuotes.value
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
    return authorQuotes.value.length > 0
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
      :quoteText="randomQuote.quoteText"
      :quoteAuthor="randomQuote.quoteAuthor"
      @clickFigcaption="getAuthorQuotes"
      @clickButton="getRandomQuote"
    />

    <!-- Several quotes from concrete author -->
    <section v-if="!seeRandom" class="author">
      <p>{{ randomQuote.quoteAuthor }}</p>
      <p v-if="!areThereSeveralQuotes">Loading quotes...</p>
      <ul>
        <QuoteItem     
          v-for="quote in noDuplicateQuotes"
          :quoteText="quote"
        />
      </ul>
      <!-- Pagination will be visible if there is more than 1 page and once the quotes have been loaded -->
      <div v-if="areThereSeveralQuotes && totalPages > 1" class="author__pagination">
        <button
          v-if="authorPage > 1"
          type="button"
          @click="goToPreviousPage"
        >&larr;</button>
        <span>Page {{ authorPage }} of {{ totalPages }}</span>
        <button
          v-if="authorPage + 1 <= totalPages"
          type="button" 
          @click="goToNextPage"      
        >&rarr;</button>
      </div>
      <button      
        type="button"
        class="button"
        @click="resetAuthorPage"
      >New quote</button>
    </section>
  </main>
</template>

<style scoped></style>
