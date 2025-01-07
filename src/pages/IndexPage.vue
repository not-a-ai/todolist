<template>
  <q-page padding>
    <div class="q-pa-md">
      <q-card>
        <q-card-section>
          <div class="text-h5">ToDo List</div>
        </q-card-section>

        <q-card-section>
          <q-input v-model="newTask.title" label="Atividade" filled />
          <q-input v-model="newTask.description" label="Descrição" filled class="q-mt-sm" />
          <div class="q-pa-md row">
            <div class="q-mt-md col-4">Prioridade:
              <q-radio
                v-model="newTask.priority"
                val="normal"
                label="Normal"
                color="primary"
              />
              <q-radio
                v-model="newTask.priority"
                val="alta"
                label="Alta"
                color="red"
              />
            </div>

            <div class="q-mt-md col-4">
              <label>Status:</label>
              <q-option-group
                v-model="newTask.status"
                :options="statuses"
                type="radio"
                inline
              />
            </div>

            <div class="q-mt-md col-4">
              <q-input
                v-model="newTask.dueDate"
                type="date"
                label="Data de Conclusão"
                filled
              />
            </div>
          </div>
          <q-btn @click="addTask" label="Adicionar Tarefa" color="primary" class="q-mt-md" />
        </q-card-section>

        <q-separator class="q-my-md" />
        <q-card-section>
          <div class="row" >
            <div class=" items-center col-5 q-mr-auto">
            <q-select
              v-model="filter"
              :options="filters"
              label="Filtrar Tarefas"
              outlined
              class="col-6"
            />
          </div>
          <div class=" items-center col-5 ">
            <q-select
              v-model="order"
              :options="orders"
              label="Ordenar Tarefas"
              outlined
              class="col-6"
            />
          </div>
          </div>
          
          <q-list bordered>
            <q-item v-for="task in filteredTasks" :key="task.id" clickable>
              <q-item-section class=" q-ma-sm color full-width" style="background-color: aquamarine;">
                <div class="col la-align-center">
                  <b class="text-center">{{ task.title }}</b>
                  <p>{{ task.description }}</p>

                  <div class="row flex-center">
                    <p class="col-5 text-center">Status: {{ task.status }}</p>
                    <p class="col-5 text-center">Prioridade: {{ task.priority }}</p>
                  </div>

                </div>
                <div class="text-caption text-grey-6">
                  Criada em: {{ task.createdAt }}
                  Prazo: {{ task.dueDate }}
                </div>
                <div v-if="task.dueDate">
                  
                </div>
              </q-item-section>
              <q-item-section side>
                <q-btn flat icon="check" @click="toggleTaskStatus(task)" />
                <q-btn flat icon="delete" color="negative" @click="deleteTask(task.id)" />
              </q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script>
export default {
  data() {
    return {
      newTask: {
        title: "",
        description: "",
        priority: "",
        status: "",
        dueDate: "",
      },
      tasks: [],
      priorities: [
        { label: "Prioridade Normal", value: "normal" },
        { label: "Prioridade Alta", value: "high" },
      ],
      statuses: [
        { label: "A fazer", value: "a fazer" },
        { label: "Fazendo", value: "fazendo" },
        { label: "Concluído", value: "feito" },
      ],
      filter: "Todas",
      filters: [
        { label: "Todas", value: "all" },
        { label: "A fazer", value: "todo" },
        { label: "Fazendo", value: "doing" },
        { label: "Feito", value: "done" },
        { label: "Alta prioridade", value: "high" },
      ],
      order: "",
      orders: [

        { label: "Por data", value: "date"},
        { label: "Por título", value: "name"},
      ]
    };
  },
  computed: {
  filteredTasks() {
    let filtered = [];

    if (this.filter === "all") {
      filtered = this.tasks;
    } else if (this.filter === "pending") {
      filtered = this.tasks.filter((task) => task.status === "doing");
    } else if (this.filter === "completed") {
      filtered = this.tasks.filter((task) => task.completed);
    } else if (this.filter === "highPriority") {
      filtered = this.tasks.filter((task) => task.priority === "high" );
    }
    if (this.order === "date") {
      return filtered.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
    } else if (this.order === "name") {
      return filtered.sort((a, b) => a.title.localeCompare(b.title));
    } else {
      filtered = this.tasks;
    }
    console.log("Filtered tasks:", filtered); 
    return filtered;
  }
},
  methods: {
    addTask() {
      if (this.newTask.title.trim() === "") {
        this.$q.notify({
          type: "negative",
          message: "O título é obrigatório!",
        });
        return;
      }

      const task = {
        id: Date.now(),
        title: this.newTask.title,
        description: this.newTask.description,
        createdAt: new Date().toLocaleString(),
        status: this.newTask.status,
        priority: this.newTask.priority,
        dueDate: this.newTask.dueDate,
      };

      this.tasks.push(task);

      this.$q.notify({
        type: "positive",
        message: "Tarefa adicionada com sucesso!",
      });
      console.log(this.newTask);
      console.log(this.tasks);
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
      this.$q.notify({
        type: "info",
        message: "Tarefa removida!",
      });
    },
    toggleTaskStatus(task) {
      task.completed = !task.completed;
      this.$q.notify({
        type: task.completed ? "positive" : "warning",
        message: `Tarefa marcada como ${task.completed ? "concluída" : "pendente"}!`,
      });
    },
  }
  
}
</script>

<style scoped>
.text-h5 {
  font-weight: bold;
  text-align: center;
}
</style>
