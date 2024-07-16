<template>
    <div>
        <!-- Título de la lista de tareas -->
        <h1>Tareas</h1>
        <!-- Formulario para agregar nuevas tareas -->
        <form @submit.prevent="addTodo()">
            <!-- Entrada de texto para nuevas tareas -->
            <el-input placeholder="todo" v-model="todo"></el-input>
        </form>
        <el-row :gutter="12">
            <!-- Componente TodoItem para mostrar tareas -->
            <TodoItem v-for="(todo, index) in todos" :key="index" :index="index" :todo="todo" @close-todo="removeTodo" />
            <!-- Área de visualización de problemas desde GitHub -->
            <el-col :span="12" v-for="(issue, index) in issues" :key="issue.id">
                <el-card class="box-card" shadow="hover" style="margin: 5px 0;">
                    <el-row :gutter="12">
                        <!-- Título del problema -->
                        <el-col :span="21">{{ issue.title }}</el-col>
                        <!-- Botón para cerrar el problema en GitHub -->
                        <el-col :span="3">
                            <el-button @click="closeIssue(index)" type="success" icon="el-icon-check" circle></el-button>
                        </el-col>
                    </el-row>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
import axios from 'axios';
import TodoItem from '@/components/TodoItem.vue';

// Configuración del cliente Axios para hacer solicitudes a la API de GitHub
const client = axios.create({
    baseURL: `${process.env.VUE_APP_GITHUB_ENDPOINT}`,
    headers: {
        'Authorization': `token ${process.env.VUE_APP_GITHUB_TOKEN}`,
        'Accept': 'application/vnd.github.v3+json',
        'Content-Type': 'application/json',
    },
})

export default {

    name: 'TodosIssues',

    components: {
        TodoItem
    },

    data() {
      // Datos del componente (estado)
        return {
          todo: '',        // Nueva tarea a agregar
          todos: [],       // Lista de tareas locales
          issues: []       // Lista de problemas obtenidos de GitHub
        }
    },

    methods: {
      // Agregar una nueva tarea
        addTodo() {
            this.todos.push(this.todo);
          // Guardar la lista de tareas en el almacenamiento local
            localStorage.setItem('todos', JSON.stringify(this.todos));
          this.todo = '';  // Limpiar el campo de entrada
        },

      // Eliminar una tarea
        removeTodo(index) {
            this.todos.splice(index, 1);
          // Actualizar el almacenamiento local con la lista modificada de tareas
            localStorage.setItem('todos', JSON.stringify(this.todos));
        },

      // Cerrar un problema en GitHub
        closeIssue(index) {
            const target = this.issues[index];
          // Hacer una solicitud PATCH a la API de GitHub para cerrar el problema
            client.patch(`/issues/${target.number}`, {
                state: 'closed'
            })
                .then(() => {
                  // Eliminar el problema cerrado de la lista local
                    this.issues.splice(index, 1);
                });
        },

      // Obtener la lista de problemas desde GitHub
        getIssues() {
            client.get('issues')
                .then((res) => {
                  // Almacenar la lista de problemas en la propiedad 'issues'
                    this.issues = res.data
                })
        }
    },

  // Método ejecutado al crear la instancia del componente
    created() {
      // Obtener las tareas almacenadas en el almacenamiento local o inicializar la lista
        this.todos = JSON.parse(localStorage.getItem('todos')) || [];
      // Obtener la lista de problemas desde GitHub
        this.getIssues();
    }
}
</script>