<template>
    <div v-if="currentTodo" class="edit-form">
        <h4>Todo</h4>
        <form>
            <div class="form-group">
                <label for="title">Title</label>
                <input type="text" class="form-control" id="title" v-model="currentTodo.title" />
            </div>
            <div class="form-group">
                <label for="description">Description</label>
                <input type="text" class="form-control" id="description" v-model="currentTodo.description" />
            </div>

            <div class="form-group">
                <label><strong>Status:</strong></label>
                {{ currentTodo.published ? "Done" : "Undone" }}
            </div>
        </form>

        <button class="btn btn-primary me-2" v-if="currentTodo.published" @click="updatePublished(false)">
            Done
        </button>
        <button v-else class="btn btn-primary me-2" @click="updatePublished(true)">
            Undone
        </button>

        <button class="btn btn-danger me-2" @click="deleteTodo">
            Delete
        </button>

        <button type="submit" class="btn btn-success me-2" @click="updateTodo">
            Update
        </button>
        <p>{{ message }}</p>
    </div>

    <div v-else>
        <br />
        <p>Please click on a Todo...</p>
    </div>
</template>

<script>
import TodoDataService from "../services/TodoDataService";

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: "todos",
    data() {
        return {
            currentTodo: null,
            message: ''
        };
    },
    methods: {
        getTodo(id) {
            TodoDataService.get(id)
                .then(response => {
                    this.currentTodo = response.data;
                    console.log(response.data);
                })
                .catch(e => {
                    console.log(e);
                });
        },

        updatePublished(status) {
            var data = {
                id: this.currentTodo.id,
                title: this.currentTodo.title,
                description: this.currentTodo.description,
                published: status
            };

            TodoDataService.update(this.currentTodo.id, data)
                .then(response => {
                    this.currentTodo.published = status;
                    console.log(response.data);
                })
                .catch(e => {
                    console.log(e);
                });
        },

        updateTodo() {
            TodoDataService.update(this.currentTodo.id, this.currentTodo)
                .then(response => {
                    console.log(response.data);
                    this.message = 'The Todo was updated successfully!';
                })
                .catch(e => {
                    console.log(e);
                });
        },

        deleteTodo() {
            TodoDataService.delete(this.currentTodo.id)
                .then(response => {
                    console.log(response.data);
                    this.$router.push({ name: "Todos" });
                })
                .catch(e => {
                    console.log(e);
                });
        }
    },
    mounted() {
        this.message = '';
        this.getTodo(this.$route.params.id);
    }
};
</script>

<style>
.edit-form {
    max-width: 300px;
    margin: auto;
}
</style>
