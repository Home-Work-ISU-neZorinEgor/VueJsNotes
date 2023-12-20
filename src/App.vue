<template>
  <div id="app" class="container mt-5">
    <div class="card p-4">
      <h1 class="mb-4">Управление заметками</h1>

      <form @submit.prevent="addNote">

        <div class="mb-3">
          <label for="noteText" class="form-label">Текст заметки:</label>
          <input type="text" id="noteText" v-model="newNoteText" class="form-control">
        </div>

        <div class="mb-3">
          <label class="form-label">Тип заметки:</label>
          <div class="form-check">
            <input type="radio" id="personal" value="personal" v-model="newNoteType" class="form-check-input">
            <label for="personal" class="form-check-label">Личная</label>
          </div>
          <div class="form-check">
            <input type="radio" id="work" value="work" v-model="newNoteType" class="form-check-input">
            <label for="work" class="form-check-label">Рабочая</label>
          </div>
        </div>

        <div class="mb-3">
          <label class="form-label">Приоритет:</label>
          <div class="input-group mb-3">
            <select v-model="newNotePriority" class="form-select">
              <option value="low">Низкий</option>
              <option value="medium">Средний</option>
              <option value="high">Высокий</option>
            </select>
            <div class="input-group-append">
              <span class="input-group-text" v-if="newNotePriority === 'low'">🟢</span>
              <span class="input-group-text" v-if="newNotePriority === 'medium'">🟡</span>
              <span class="input-group-text" v-if="newNotePriority === 'high'">🔴</span>
            </div>
          </div>
        </div>

        <button type="submit" class="btn btn-primary" :disabled="!newNoteText.trim()">Добавить заметку</button>
        <button type="button" @click="resetData" class="btn btn-danger ms-2">Сбросить данные</button>

      </form>

      <ul class="list-group mt-3">
        <li v-for="(note, index) in sortedNotes" :key="index" class="list-group-item">
          <div class="d-flex justify-content-between align-items-center">
            <div :class="{ 'text-decoration-line-through': note.completed }">
              <strong>{{ note.type }}</strong> - {{ note.text }} (Приоритет: {{ note.priority }}) - {{ note.completed ? 'Завершено' : 'Не завершено' }}
            </div>
            <div>
              <input type="checkbox" v-model="note.completed" class="form-check-input" id="completedItem">
              <label for="completedItem" class="form-check-label ms-2">Завершено</label>
              <button @click="deleteNote(index)" class="btn btn-danger ms-2">Удалить</button>
            </div>
          </div>
        </li>
      </ul>

    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newNoteText: '',
      newNoteType: 'personal',
      newNotePriority: 'low',
      newNoteCompleted: false,
      notes: JSON.parse(localStorage.getItem('notes')) || [],
    };
  },
  computed: {
    sortedNotes() {
      return this.notes.slice().sort((a, b) => {
        const priorityOrder = { 'low': 1, 'medium': 2, 'high': 3 };
        const priorityComparison = priorityOrder[b.priority] - priorityOrder[a.priority];
        return priorityComparison !== 0 ? priorityComparison : (b.completed - a.completed);
      });
    },
  },
  methods: {
    addNote() {
      if (this.newNoteText.trim() === '') {
        // Prevent adding empty notes
        return;
      }

      const newNote = {
        text: this.newNoteText,
        type: this.newNoteType,
        priority: this.newNotePriority,
        completed: this.newNoteCompleted,
      };

      this.notes.push(newNote);
      this.saveData();
      this.resetForm();
    },
    saveData() {
      localStorage.setItem('notes', JSON.stringify(this.notes));
    },
    resetForm() {
      this.newNoteText = '';
      this.newNoteType = 'personal';
      this.newNotePriority = 'low';
      this.newNoteCompleted = false;
    },
    resetData() {
      localStorage.removeItem('notes');
      this.notes = [];
    },
    deleteNote(index) {
      this.notes.splice(index, 1);
      this.saveData();
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

button {
  margin-top: 10px;
}
</style>
