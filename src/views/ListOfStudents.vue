<template>
  <v-sheet border rounded>
    <v-data-table :headers="headers" :hide-default-footer="students.length < 11" :items="students">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>
            <v-icon
              color="medium-emphasis"
              icon="mdi-student-multiple"
              size="x-small"
              start
            ></v-icon>
            Students
          </v-toolbar-title>

          <v-btn
            class="me-2"
            prepend-icon="mdi-plus"
            rounded="lg"
            text="Add a Student"
            border
            @click="add"
          ></v-btn>
        </v-toolbar>
      </template>

      <template v-slot:[`item.name`]="{ value }">
        <v-chip :text="value" border="thin opacity-25" prepend-icon="mdi-student" label>
          <template v-slot:prepend>
            <v-icon color="medium-emphasis"></v-icon>
          </template>
        </v-chip>
      </template>

      <template v-slot:[`item.actions`]="{ item }">
        <div class="d-flex ga-2 justify-end">
          <v-icon
            color="medium-emphasis"
            icon="mdi-pencil"
            size="small"
            @click="edit(item.id)"
          ></v-icon>

          <v-icon
            color="medium-emphasis"
            icon="mdi-delete"
            size="small"
            @click="remove(item.id)"
          ></v-icon>
        </div>
      </template>

      <template v-slot:no-data>
        <v-btn
          prepend-icon="mdi-backup-restore"
          rounded="lg"
          text="Reset data"
          variant="text"
          border
          @click="reset"
        ></v-btn>
      </template>
    </v-data-table>
  </v-sheet>

  <v-dialog v-model="dialog" max-width="500">
    <v-card
      :subtitle="`${isEditing ? 'Update' : 'Create'} student`"
      :title="`${isEditing ? 'Edit' : 'Add'} a student`"
    >
      <template v-slot:text>
        <v-row>
          <v-col cols="12">
            <v-text-field v-model="record.name" label="Name"></v-text-field>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field v-model="record.surname" label="Surname"></v-text-field>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field v-model="record.middlename" label="Middlename"></v-text-field>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field v-model="record.phone" label="Phone"></v-text-field>
          </v-col>

          <v-col cols="12" md="6">
            <v-text-field v-model="record.email" label="Email"></v-text-field>
          </v-col>

          <v-col cols="12">
            <v-text-field v-model="record.year" label="Year"></v-text-field>
          </v-col>
        </v-row>
      </template>

      <v-divider></v-divider>

      <v-card-actions class="bg-surface-light">
        <v-btn text="Cancel" variant="plain" @click="dialog = false"></v-btn>

        <v-spacer></v-spacer>

        <v-btn text="Save" @click="save"></v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

interface Student {
  id: number
  name: string
  surname: string
  middlename: string
  year: string
  phone: string
  email: string
}

const DEFAULT_RECORD: Student = {
  id: 0,
  name: '',
  surname: '',
  middlename: '',
  year: '',
  phone: '',
  email: '',
}

const students = ref([] as Student[])
const record = ref(DEFAULT_RECORD)
const dialog = ref(false)
const isEditing = ref(false)

const headers = [
  { title: 'Name', key: 'name', align: 'start' as const },
  { title: 'Surname', key: 'surname', align: undefined },
  { title: 'Middlename', key: 'middlename', align: undefined },
  { title: 'Year', key: 'year', align: 'end' as const },
  { title: 'Phone', key: 'phone', align: 'end' as const },
  { title: 'Email', key: 'email', align: 'end' as const },
  { title: 'Actions', key: 'actions', align: 'end' as const, sortable: false },
]

onMounted(() => {
  reset()
})

function add() {
  isEditing.value = false
  record.value = DEFAULT_RECORD
  dialog.value = true
}

function edit(id: number) {
  isEditing.value = true

  const found = students.value.find((student) => student.id === id)

  if (found) {
    record.value = {
      id: found.id,
      name: found.name,
      surname: found.surname,
      middlename: found.middlename,
      year: found.year,
      phone: found.phone,
      email: found.email,
    }

    dialog.value = true
  }
}

function remove(id: number) {
  const index = students.value.findIndex((student) => student.id === id)
  students.value.splice(index, 1)
}

function save() {
  if (isEditing.value) {
    const index = students.value.findIndex((student) => student.id === record.value.id)
    students.value[index] = record.value
  } else {
    record.value.id = students.value.length + 1
    students.value.push(record.value)
  }

  dialog.value = false
}

function reset() {
  dialog.value = false
  record.value = DEFAULT_RECORD
  students.value = [
    {
      id: 1,
      name: 'John',
      surname: 'Doe',
      middlename: 'Middle',
      year: '1990',
      phone: '+1 (123) 456-7890',
      email: 'john.doe@example.com',
    },
    {
      id: 2,
      name: 'Jane',
      surname: 'Doe',
      middlename: 'Middle',
      year: '1991',
      phone: '+1 (987) 654-3210',
      email: 'jane.doe@example.com',
    },
    {
      id: 3,
      name: 'Richard',
      surname: 'Roe',
      middlename: 'Middle',
      year: '1995',
      phone: '+1 (555) 123-4567',
      email: 'richard.roe@example.com',
    },
  ]
}
</script>
