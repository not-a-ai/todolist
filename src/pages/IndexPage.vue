<template>
  <q-page padding>
    <div class="q-pa-md">
      <q-card>
        <q-card-section>
          <div class="text-h5">Nova tarefas</div>
        </q-card-section>

        <q-card-section>
          <q-input v-model="newTask.title" label="Atividade" filled />
          <q-input v-model="newTask.description" label="Descrição" filled class="q-mt-sm" />
          <div class="q-pa-md row">
            <div class="q-mt-md col-6">
              <p>Prioridade:</p>
              <q-checkbox
                v-model="newTask.priority"
                label="Alta"
                color="blue"
              />
            </div>

            <div class="q-mt-md col-6">
              <label>Status:</label>
              <q-option-group
                v-model="newTask.status"
                :options="statuses"
                type="radio"
                inline
              />
            </div>


          </div>
          <q-btn @click="addTask" label="Adicionar Tarefa" color="primary" class="q-mt-md" />
        </q-card-section>

        <q-separator class="q-my-md" />
        <div class="text-h5">Tarefas</div>
        <q-card-section>
          <div class="row" >
            <div class=" items-center col-4 q-mr-auto">
            <q-select
              v-model="filter"
              :options="filters"
              label="Filtrar Tarefas"
              class="col-6"
            />
          </div>
          <div class=" items-center col-4 ">
            <q-select
              v-model="order"
              :options="orders"
              label="Ordenar Tarefas"
              class="col-6"
            />
          </div>
          </div>
          
          <q-list>
            <q-item v-for="task in filteredTasks" :key="task.id" clickable class="q-ma-lg" id="card-task">
              <q-item-section class=" q-ma-sm color full-width" id="task">
                <div class="col la-align-center">
                  <b class="text-center">{{ task.title }}</b>
                  <q-separator class="q-my-md" />
                  <p>{{ task.description }}</p>

                  <div class="row">
                    <p class="col-5 ">Status: {{task.status ?  task.status :  "-" }}</p>
                    <p class="col-5 ">Prioridade: {{task.priority === true ? "Alta" :  "Normal" }}</p>
                  </div>
                </div>
                <q-separator class="q-my-md-sm" />
                <div class="text-caption text-grey-6">
                  Criada em: {{ task.createdAt }}
                </div>
                <div class="text-caption text-grey-6">
                  Concluída em: {{ task.finishDate ? task.finishDate :  "-"}}
                </div>
              </q-item-section>
              <q-item-section side>
                <q-btn flat icon="stop" @click="task.status = 'a fazer'"></q-btn>
                <q-btn flat icon="start" @click="task.status = 'fazendo'"></q-btn>
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

<script setup>
  import { ref, reactive, computed } from "vue";
  import { useQuasar } from "quasar";

  // Quasar Notify
  const $q = useQuasar();

  // Reactive states
  const newTask = reactive({
    title: "",
    description: "",
    priority: "",
    status: "",
    finishDate: "",
  });

  const tasks = ref([
  {
    id: 1,
    title: "Finalizar projeto Vue.js",
    description: "Concluir as funcionalidades principais",
    priority: "alta",
    status: "a fazer",
    createdAt: "01/01/2025, 10:00:00",
    finishDate: "30/01/2025, 10:00:00",
  },
  {
    id: 2,
    title: "Revisar artigo de IA",
    description: "Aprimorar o texto e adicionar referências",
    priority: "normal",
    status: "fazendo",
    createdAt: "03/01/2025, 15:30:00",
    finishDate: "",
  },
  {
    id: 3,
    title: "Preparar apresentação",
    description: "Criar slides para o seminário",
    priority: "alta",
    status: "concluido",
    createdAt: "02/01/2025, 09:00:00",
    finishDate: "20/02/2025, 10:00:00",
  },
  {
    id: 4,
    title: "Estudar DevOps",
    description: "Ler documentação sobre Kubernetes e Docker",
    priority: "normal",
    status: "a fazer",
    createdAt: "05/01/2025, 08:00:00",
    finishDate: "",
  },
  ]);

  const statuses = ref([
    { label: "A fazer", value: "a fazer" },
    { label: "Fazendo", value: "fazendo" },
  ]);

  const filter = ref("");

  const filters = ref([
    { label: "Todas", value: "todas" },
    { label: "A fazer", value: "a fazer" },
    { label: "Fazendo", value: "fazendo" },
    { label: "Concluido", value: "concluido" },
    { label: "Alta prioridade", value: "alta" },
  ]);

  const order = ref("");

  const orders = ref([
    { label: "Por data", value: "Por data" },
    { label: "Por título", value: "Por nome" },
  ]);

  const filteredTasks = computed(() => {
    let filtered = new Array(...tasks.value);
    let filterValue = filter.value.value;

    if (filterValue === "todas") {
      filtered = tasks.value;
    } else if (filterValue === "a fazer") {
      filtered = tasks.value.filter((task) => task.status === "a fazer");
    } else if (filterValue === "fazendo") {
      filtered = tasks.value.filter((task) => task.status === "fazendo");
    } else if (filterValue === "concluido") {
      filtered = tasks.value.filter((task) => task.status === "concluido");
    } else if (filterValue === "alta") {
      filtered = tasks.value.filter((task) => task.priority === "alta");
    }
    console.log(order.value.value);
    if (order.value.value === "Por data") {
      filtered.sort((a, b) => new Date(a.createdAt) - new Date(b.createdAt));
    } else if (order.value.value === "Por nome") {
      filtered.sort((a, b) => a.title.localeCompare(b.title));
    }
      console.log(filtered)
      return filtered;
  });

  // Methods
  const addTask = () => {
    if (newTask.title.trim() === "") {
      $q.notify({
        type: "negative",
        message: "O título é obrigatório!",
      });
      return;
    }

    const task = {
      id: Date.now(),
      title: newTask.title,
      description: newTask.description,
      createdAt: new Date().toLocaleString(),
      status: newTask.status,
      priority: newTask.priority,
      finishDate: newTask.finishDate,
    };

    tasks.value.push(task);

    $q.notify({
      type: "positive",
      message: "Tarefa adicionada com sucesso!",
    });

    // Reset the newTask
    newTask.title = "";
    newTask.description = "";
    newTask.priority = "";
    newTask.status = "";
    newTask.dueDate = "";
  };

  const deleteTask = (id) => {
    tasks.value = tasks.value.filter((task) => task.id !== id);
    $q.notify({
      type: "info",
      message: "Tarefa removida!",
    });
  };

  const toggleTaskStatus = (task) => {
    task.status = "Concluido";
    task.finishDate = new Date().toLocaleString();
    $q.notify({
      type: task.completed ? "positive" : "warning",
      message: `Tarefa marcada como ${
        task.status}!`,
    });
  };
</script>

<style scoped>
.text-h5 {
  font-weight: bold;
  text-align: center;
}
#card-task {
  border: 0.5rem solid #1976d218;
  border-radius: 1rem;
}
#task {
  padding: 1rem;

}
</style>
