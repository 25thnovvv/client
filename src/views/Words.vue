<template>
  <div class="words-container">
    <div class="header-section">
      <h1>Vocabulary List</h1>
      <button class="delete-all-btn" @click.prevent="onDeleteAll()">
        <i class="trash alternate icon"></i>
        Delete All
      </button>
    </div>

    <div class="table-container">
      <table class="modern-table">
        <thead>
          <tr>
            <th>
              <div class="header-cell">
                <i class="united kingdom flag"></i>
                English
              </div>
            </th>
            <th>
              <div class="header-cell">
                <i class="germany flag"></i>
                German
              </div>
            </th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(word, i) in words" :key="i">
            <td>
              <span class="word-text english-text">{{ word.english }}</span>
            </td>
            <td>
              <span class="word-text german-text">{{ word.german }}</span>
            </td>
            <td>
              <div class="action-buttons">
                <router-link class="action-btn view" :to="{ name: 'Show', params: { id: word._id } }">
                  <i class="eye icon"></i>
                </router-link>
                <router-link class="action-btn edit" :to="{ name: 'Edit', params: { id: word._id } }">
                  <i class="edit icon"></i>
                </router-link>
                <button class="action-btn delete" @click.prevent="onDelete(word._id)">
                  <i class="trash icon"></i>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ViewAllVocabs, DeleteVocab, DeleteAllVocabs } from '../helpers/api.js'

export default {
  name: "Words",
  data() {
    return {
      words: [], // Initialize an empty array to store words
    };
  },
  async mounted() {
    // Fetch all vocabulary words when the component is mounted
    this.words = await ViewAllVocabs();
  },
  methods: {
    async onDelete(id) {
      // Display a confirmation message before deletion
      const deleteConfirm = window.confirm(
        "Are you sure to delete this word ?"
      );
      if (deleteConfirm) {
        // Delete the word from the database
        await DeleteVocab(id);
        // Refresh the word list by removing the deleted word
        const updatedWords = this.words.filter((word) => word._id !== id);
        this.words = updatedWords;
        // Display a flash message after deletion
        this.flash("Delete word succeed !");
      }
    },
    async onDeleteAll() {
      // Display a confirmation message before deletion
      const deleteConfirm = window.confirm(
        "Are you sure to delete all words ?"
      );
      if (deleteConfirm) {
        // Delete all words from the database
        await DeleteAllVocabs();
        // Refresh the word list by clearing it
        this.words = [];
        // Display a flash message after deletion
        this.flash("Delete all word succeed !");
      }
    },
  },
}
</script>

<style scoped>
.words-container {
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

.header-section {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.header-section h1 {
  font-size: 2.5rem;
  color: #2c3e50;
  margin: 0;
}

.delete-all-btn {
  background-color: #e74c3c;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
}

.delete-all-btn:hover {
  background-color: #c0392b;
  transform: translateY(-2px);
}

.table-container {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.modern-table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
}

.modern-table th {
  background-color: #f8f9fa;
  padding: 15px;
  text-align: left;
  font-weight: 600;
  color: #2c3e50;
  border-bottom: 2px solid #eee;
}

.modern-table td {
  padding: 15px;
  border-bottom: 1px solid #eee;
  vertical-align: middle;
}

.modern-table tr:last-child td {
  border-bottom: none;
}

.modern-table tr:hover {
  background-color: #f8f9fa;
}

.word-cell {
  display: flex;
  align-items: center;
  gap: 10px;
}

.action-buttons {
  display: flex;
  gap: 8px;
  justify-content: flex-start;
}

.action-btn {
  border: none;
  padding: 8px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
}

.action-btn.view {
  background-color: #3498db;
  color: white;
}

.action-btn.edit {
  background-color: #f39c12;
  color: white;
}

.action-btn.delete {
  background-color: #e74c3c;
  color: white;
}

.action-btn:hover {
  transform: scale(1.1);
}

.word-text {
  font-size: 1.2rem;
  letter-spacing: 0.3px;
  font-weight: 500;
  padding: 6px 10px;
  border-radius: 4px;
  transition: all 0.2s ease;
  display: inline-block;
}

.english-text {
  background-color: #e3f2fd;
  color: #1565c0;
}

.german-text {
  background-color: #fff3e0;
  color: #e65100;
}

.vietnamese-text {
  background-color: #e8f5e9;
  color: #2e7d32;
}

.modern-table tr:hover .word-text {
  transform: scale(1.05);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.header-cell {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 1.1rem;
}

@media (max-width: 600px) {
  .table-container {
    overflow-x: auto;
  }
  
  .modern-table {
    min-width: 600px;
  }
  
  .header-section {
    flex-direction: column;
    gap: 20px;
    text-align: center;
  }
}
</style>