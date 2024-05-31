<template>
  <div class="container">
    <div class="left-pane">
      <form @submit.prevent="save" class="form">
        <h2>Tugas 6</h2>
        <input
          type="text"
          v-model="form.title"
          placeholder="Title"
          class="input"
        />
        <textarea
          v-model="form.content"
          placeholder="Content"
          class="textarea"
        ></textarea>
        <button type="submit" class="button save-button">Save</button>
      </form>
      <button @click="load" class="button load-button">Load Articles</button>
    </div>
    <div class="right-pane">
      <ul class="article-list">
        <li v-for="article in articles" :key="article.id" class="article-item">
          <div class="left-button-group">
            <button @click="edit(article)" class="button edit-button">
              Edit
            </button>
          </div>
          <div class="article-content">
            <h3>{{ article.title }}</h3>
            <p>{{ article.content }}</p>
          </div>
          <div class="right-button-group">
            <button @click="remove(article.id)" class="button delete-button">
              Delete
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const form = reactive({
      id: null,
      title: "",
      content: "",
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get("http://localhost:3000/articles");
        articles.value = response.data;
      } catch (error) {
        console.error("Error loading articles:", error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : "http://localhost:3000/articles";
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          const index = articles.value.findIndex(
            (article) => article.id === form.id
          );
          articles.value[index] = response.data;
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = "";
        form.content = "";
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  },
};
</script>

<style scoped>
.container {
  display: flex;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.left-pane {
  width: 300px;
  position: fixed;
  top: 200px;
  left: 150px;
  padding: 50px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form h2 {
  margin-bottom: 10px;
}

.input,
.textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #dddddd00;
  backdrop-filter: blur(10px);
  color: black;
}

.input::placeholder,
.textarea::placeholder {
  color: rgb(0, 0, 0);
}

.input::placeholder:focus,
.textarea::placeholder:focus {
  color: rgb(255, 255, 255);
}

.input:focus,
.textarea:focus {
  background-color: rgba(0, 0, 0, 0.219);
  box-shadow: 0 2px 4px rgb(0, 0, 0);
  color: rgb(255, 255, 255);
}

.button {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-top: 5px;
  transition: background 0.3s ease;
}

.save-button {
  background: #ffffff00;
  color: #ffffff;
  backdrop-filter: blur(10px);
}

.save-button:hover {
  box-shadow: 0 2px 4px rgb(0, 0, 0);
  background: #12d02bc8;
  color: black;
}

.load-button {
  background: #2195f300;
  color: #fff;
  width: 100%;
  margin-top: 10px;
  backdrop-filter: blur(10px);
}

.load-button:hover {
  box-shadow: 0 2px 4px rgb(0, 0, 0);
  background: #0b7ddacd;
  color: black;
}

.right-pane {
  margin-left: 340px;
  width: calc(100% - 340px);
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  background: #ffffff00;
  padding: 20px;
  margin-bottom: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-left: 150px;
}

.article-content {
  flex: 1;
  margin: 0 20px;
  color: black;
}

.article-content h3 {
  margin: 0 0 10px;
  color: black;
}

.left-button-group {
  display: flex;
  flex-direction: column;
  margin-right: 20px;
}

.right-button-group {
  display: flex;
  flex-direction: column;
  margin-left: 20px;
}

.edit-button {
  background: #ff990000;
  color: #fff;
  margin-right: 100px;
  backdrop-filter: blur(10px);
}

.edit-button:hover {
  box-shadow: 0 2px 4px rgb(0, 0, 0);
  background: #fb8a00d3;
  color: black;
}

.delete-button {
  background: #f4433600;
  color: #fff;
  margin-left: 100px;
  backdrop-filter: blur(10px);
}

.delete-button:hover {
  background: #e53835d2;
  box-shadow: 0 2px 4px rgb(0, 0, 0);
  color: black;
}
</style>
