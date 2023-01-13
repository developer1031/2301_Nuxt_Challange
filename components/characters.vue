<template>
<v-container>
  <v-form>
    <v-row>
      <!-- <v-col cols="4">
        <v-select
          label="Filter by Status"
          :items="STATUS"
          v-model="filterStatus"
        ></v-select>
      </v-col>
      <v-col cols="4">
        <v-select
          label="Filter by Gender"
          :items="GENDERS"
          v-model="filterGender"
        ></v-select>
      </v-col> -->
      <v-col cols="4">
        <v-text-field
          v-model="filterName"
          label="Character Name"
        ></v-text-field>
      </v-col>
    </v-row>
    <v-btn
      color="warning"
      @click="onPage"
    >
      検索
    </v-btn>
  </v-form>

  <v-table>
    <thead>
      <tr>
        <th class="text-left">
          name
        </th>
        <th class="text-left">
          species
        </th>
        <th class="text-left">
          status
        </th>
        <th class="text-left">
          gender
        </th>
        <th class="text-left">
          origin.name
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        :key="item.id"
        v-for="item in results"
        @click="dialog = true, selectedItem = item"
      >
        <td>{{ item.name }}</td>
        <td>{{ item.species }}</td>
        <td>{{ item.status }}</td>
        <td>{{ item.gender }}</td>
        <td>{{ item.origin.name }}</td>
      </tr>
    </tbody>
  </v-table>

  <v-dialog
    v-if="selectedItem != null"
    v-model="dialog"
    width="300"
  >
    <v-card>
      <v-card-text>
        <v-img
          :aspect-ratio="1 / 1"
          :width="150"
          :src="selectedItem.image"
          class="d-block mx-auto"
          cover
        ></v-img>
        <p class="text-center">{{ selectedItem.name }}</p>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" block @click="dialog = false">Close</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <div v-if="pages != null" class="text-center">
    <v-pagination
      v-model="page"
      :total-visible="7"
      :length="pages"
    ></v-pagination>
  </div>
</v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  characterIds: Array
})
const isPage = props.characterIds == null

const BASE_URL = 'https://rickandmortyapi.com/api/character'
const STATUS = [
  { value: null, title: 'All' },
  { value: 'alive', title: 'Alive' },
  { value: 'dead', title: 'Dead' },
  { value: 'unknown', title: 'Unknown' },
]
const GENDERS = [
  { value: null, title: 'All' },
  { value: 'female', title: 'Female' },
  { value: 'male', title: 'Male' },
  { value: 'genderless', title: 'Genderless' },
  { value: 'unknown', title: 'Unknown' },
]

// const route = useRoute()

const page = ref(1)
const pages = ref(null)
const results = ref([])
const selectedItem = ref(null)
const dialog = ref(false)
const filterStatus = ref(null)
const filterGender = ref(null)
const filterName = ref(null)

async function onPage() {
  const url = new URL(BASE_URL)
  url.searchParams.append('page', page.value)

  if (filterName.value) {
    url.searchParams.append('name', filterName.value)
  }
  if (filterStatus.value) {
    url.searchParams.append('status', filterStatus.value)
  }
  if (filterGender.value) {
    url.searchParams.append('gender', filterGender.value)
  }

  const { data: res } = await useFetch(url)
  if (res.value) {
    results.value = res.value.results

    if (res.value.info) {
      pages.value = res.value.info.pages
    }
  } else {
    pages.value = null
    results.value = []
  }
}

async function onComponent() {
  let url = BASE_URL

  const characterIds = Object.values(props.characterIds)
  url += `/${characterIds.join(',')}`

  const { data: res } = await useFetch(url)
  if (res.value) {
    results.value = res.value
  }
}


if (isPage) {
  onPage()
} else {
  onComponent()
}
// onMounted(() => {
  
// });

watch(() => page.value, () => onPage())
</script>
