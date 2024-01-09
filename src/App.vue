<script setup>

// Importación de funciones de Vue
import { ref, onMounted, computed, watch } from "vue";

// Las importaciones anteriores permiten utilizar características clave de Vue.js:
// - 'ref' proporciona una forma reactiva de almacenar y acceder a datos mutables.
// - 'onMounted' permite ejecutar lógica cuando un componente se monta en el DOM.
// - 'computed' permite crear propiedades computadas reactivas.
// - 'watch' proporciona la capacidad de observar cambios reactivos en los datos.

// Definición de datos reactivos
const todos = ref([]);
const nombre = ref("");

// Definición de una propiedad computada
const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
    todos.value.sort((a, b) => {
        return b.createdAt - a.createdAt;
    })
);

const agregarToDo = () => {

    // Verifica si el campo de contenido está vacío o si no se ha seleccionado una categoría
    if (input_content.value.trim() === '' || input_category.value === null) {
        // Si alguna de estas condiciones se cumple, se detiene la ejecución
        // y sale de la función actual con la instrucción 'return'
        return;
    }

    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: Date.now(),
    });

    input_content.value = "";
    input_category.value = null;

}

const eliminarToDo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo);
}


// Observa los cambios en la variable 'todos' y almacena sus nuevos valores en el almacenamiento local (localStorage)
watch(todos, (nuevoValor) => {

    // Con cada cambio en 'todos', se actualiza el almacenamiento local ('localStorage') convirtiendo el nuevo valor a una cadena JSON y almacenándolo con la clave "todos"
    localStorage.setItem("todos", JSON.stringify(nuevoValor));
}, { deep: true });


// Observa los cambios en la variable 'nombre' y actualiza su valor en el almacenamiento local (localStorage)
watch(nombre, (nuevoValor) => {

    // Cada vez que 'nombre' cambia, se actualiza su valor en el almacenamiento local  con la clave "nombre"
    localStorage.setItem("nombre", nuevoValor);
});


// Esta lógica se ejecuta cuando el componente se monta en el DOM.
onMounted(() => {

    // Obtiene el valor almacenado previamente en 'localStorage' con la clave "nombre".
    // Si existe un valor almacenado, se asigna a la variable reactiva 'nombre.value'.
    // En caso contrario, si no hay valor almacenado, se establece 'nombre.value' como una cadena vacía ("").
    nombre.value = localStorage.getItem("nombre") || "";

    // Obtiene el valor almacenado previamente en 'localStorage' con la clave "todos".
    // Si existe un valor almacenado, se asigna a la variable reactiva 'todos.value'.
    // En caso contrario, si no hay valor almacenado, se establece 'todos.value' como un array vacío ([]).
    todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

</script>

<template>

    <main class="app">
        <section class="greeting">
            <h1 class="title">
                Cómo te encuentras hoy?
                <input type="text" nombre="" id="" placeholder="Ingresa tu nombre aquí" v-model="nombre" />
            </h1>
        </section>

        <section class="create-todo">
            <h2 class="title">CREAR UNA TAREA</h2>

            <form @submit.prevent="agregarToDo">
                <h4>Qué tienes pendiente para hoy?</h4>
                <input type="text" placeholder="ej: Salir a trotar con perro." v-model="input_content" />

                <h4>Selecciona una categoría:</h4>

                <div class="options">

                    <label>
                        <input type="radio" name="category" value="Trabajo" v-model="input_category" />
                        <span class="bubble business"></span>
                        <div>Trabajo</div>
                    </label>

                    <label>
                        <input type="radio" name="category" value="Personal" v-model="input_category" />
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>

                    <label>
                        <input type="radio" name="category" value="Educativo" v-model="input_category" />
                        <span class="bubble personal"></span>
                        <div>Educativo</div>
                    </label>

                    <label>
                        <input type="radio" name="category" value="Externo" v-model="input_category" />
                        <span class="bubble business"></span>
                        <div>Externo</div>
                    </label>
                </div>

                <input type="submit" value="Agregar tarea">
            </form>
        </section>

        <section class="todo-list">
            <h2 class="title">LISTA DE PENDIENTES</h2>
            <div class="list">

                <section v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content">
                    </div>

                    <div class="actions">
                        <button class="delete" @click="eliminarToDo(todo)">Eliminar</button>
                    </div>

                </section>
            </div>
        </section>
    </main>
</template>
