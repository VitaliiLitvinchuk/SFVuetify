<template>
  <v-text-field
    label="Новий елемент"
    append-icon="mdi-plus"
    @click:append="addNewItem"
    @keyup.enter="addNewItem"
    v-model="newItemTitle"
    clearable
  ></v-text-field>

  <v-list v-model:selected="shoppingList" lines="three" select-strategy="leaf">
    <template v-for="item in shoppingList" :key="item.id">
      <v-list-item :class="{ 'bg-orange-lighten-5': item.checked }" @click="checkItem(item.id)">
        <template v-slot:prepend>
          <v-list-item-action start>
            <v-checkbox-btn :model-value="item.checked" />
          </v-list-item-action>
          <v-list-item-title :class="{ 'text-decoration-line-through': item.checked }">
            {{ item.title }}
          </v-list-item-title>
        </template>

        <template v-slot:append>
          <v-list-item-action>
            <v-btn icon @click.stop="deleteItem(item.id)">
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </v-list-item-action>
        </template>
      </v-list-item>

      <v-divider />
    </template>
  </v-list>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const shoppingList = ref([
  { id: 1, title: 'a', checked: false },
  { id: 2, title: 'b', checked: false },
  { id: 3, title: 'c', checked: false },
])

const newItemTitle = ref('')

const addNewItem = () => {
  if (newItemTitle.value.trim()) {
    const newId = shoppingList.value.length + 1
    shoppingList.value.push({ id: newId, title: newItemTitle.value, checked: false })
    newItemTitle.value = ''
  }
}

function checkItem(id: number) {
  const item = shoppingList.value.find((i) => i.id === id)
  if (item) item.checked = !item.checked
}

function deleteItem(id: number) {
  shoppingList.value = shoppingList.value.filter((item) => item.id !== id)
}
</script>
