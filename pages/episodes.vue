<template>
<v-container>
  <v-table>
    <thead>
      <tr>
        <th class="text-left">
          id
        </th>
        <th class="text-left">
          name
        </th>
        <th class="text-left">
          episode
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        :key="item.id"
        v-for="item in results"
        @click="onSelectItem(item)"
      >
        <td>{{ item.id }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.episode }}</td>
      </tr>
    </tbody>
  </v-table>

  <v-dialog
    v-if="selectedItem != null"
    v-model="dialog"
  >
    <v-card>
      <v-card-text>
        <p class="text-center">
          {{ selectedItem.name }},  {{ selectedItem.air_date }}
        </p>

        <Characters :characterIds="characterIds" />
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" block @click="dialog = false">Close</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <div class="text-center">
    <v-pagination
      v-model="page"
      :total-visible="7"
      :length="pages"
    ></v-pagination>
  </div>
</v-container>
</template>

<script setup>
import { ref } from 'vue'
import Characters from '~/components/characters.vue'

const BASE_URL = 'https://rickandmortyapi.com/api/episode'

// const route = useRoute()

const page = ref(1)
const pages = ref(0)
const results = ref([])
const selectedItem = ref(null)
const dialog = ref(false)
const characterIds = ref([])

function onSelectItem(item) {
  dialog.value = true
  selectedItem.value = item
  characterIds.value = item.characters.map(url => url.split('/').at(-1))
}

async function onPage() {
  let url = `${BASE_URL}?page=${page.value}`

  const { data: res } = await useFetch(url)
  if (res.value) {
    results.value = res.value.results
    const {info} = res.value
    pages.value = info.pages
  } else {
    results.value = []
    pages.value = 0
  }
}
onPage()

watch(() => page.value, () => onPage())
</script>
