<template>
  <div>
    <h1>Rick and Morty Characters</h1>
    <div>
      <div class="filters">
        <input v-model="filters.name" type="text" placeholder="Filter by name" />
        <select v-model="filters.status">
          <option value="">All Statuses</option>
          <option value="alive">Alive</option>
          <option value="dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select>
        <button @click="applyFilters">Apply</button>
      </div>
      <div class="pagination">
        <button @click="prevPage" :disabled="page === 1">Previous</button>
        <span>Page {{ page }}</span>
        <button @click="nextPage" :disabled="!hasNextPage">Next</button>
      </div>
      <div>
      <div class="characters">
        <CharacterCard v-for="character in characters" :key="character.id" :character="character" />
      </div>
    </div>
    </div>

    
  </div>
</template>

<script>
import axios from 'axios'
import CharacterCard from './components/CharacterCard.vue'
import { ref } from 'vue'

export default {
  name: 'App',
  components: {
    CharacterCard
  },
  setup() {
    const characters = ref([])
    const page = ref(1)
    const hasNextPage = ref(true)
    const filters = ref({
      name: '',
      status: ''
    })

    const fetchCharacters = async () => {
      try {
        const response = await axios.get('https://rickandmortyapi.com/api/character', {
          params: {
            page: page.value,
            name: filters.value.name,
            status: filters.value.status
          }
        })
        characters.value = response.data.results
        hasNextPage.value = response.data.info.next !== null
      } catch (error) {
        console.error('Error fetching characters:', error)
      }
    }

    const applyFilters = () => {
      page.value = 1
      fetchCharacters()
    }

    const nextPage = () => {
      if (hasNextPage.value) {
        page.value++
        fetchCharacters()
      }
    }

    const prevPage = () => {
      if (page.value > 1) {
        page.value--
        fetchCharacters()
      }
    }

    fetchCharacters()

    return {
      characters,
      page,
      hasNextPage,
      filters,
      applyFilters,
      nextPage,
      prevPage
    }
  }
}
</script>

<style>
h1{
  padding: 30px 0;
  font-size:56px;
}
#app {
  text-align: center;
}
.pagination{
  margin-bottom: 30px;
}
.filters {
  margin-bottom: 16px;
}
.filters input,
.filters select,
.filters button {
  margin: 0 8px;
}
.characters {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.pagination {
  margin-top: 16px;
}
</style>
