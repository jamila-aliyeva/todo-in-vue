<script setup>
import { ref, onMounted, watch } from "vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const tasks = ref([]);

const taskTitle = ref("");

// addingTask

const addTask = () => {
  if (taskTitle.value.trim().length) {
    const newTask = {
      id: Date.now(),
      title: taskTitle.value,
      isDone: false,
    };
    tasks.value.push(newTask);
    taskTitle.value = "";
    toast.success("Adding task!", {
      autoClose: 1000,
      position: "top-center",
    });
  }
};

// deletingTask

const deleting = (id) => {
  const task = tasks.value.filter((el) => el.id !== id);
  tasks.value = task;
  toast("Task deleted !", {
    autoClose: 1000,
    position: "top-center",
  });
};

// isDone

const isDone = (id) => {
  tasks.value.forEach((el) => {
    if (el.id == id) {
      el.isDone = true;
      toast.success("Set status!", {
        autoClose: 1000,
        position: "top-center",
      });
    }
  });
};

// savingtoLocalStorage

const savingLocalStorage = () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
};

onMounted(() => {
  const savedTasks = localStorage.getItem("tasks");
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});

watch(tasks, savingLocalStorage, { deep: true });
</script>

<template>
  <div class="conatainer">
    <div
      class="max-w-lg min-h-[60vh] bg-[#f0f8ff] mx-auto my-12 p-12 rounded-2xl border-4 border-white"
    >
      <form @submit.prevent="addTask" class="flex gap-2" action="#">
        <input
          v-model="taskTitle"
          class="p-2 flex-grow border border-[#3d3d3d] rounded-l-lg"
          type="text"
          placeholder="Enter task title"
        />
        <button type="submit" class="border-none rounded-lg p-2 bg-[#005CAD] text-white">
          add task
        </button>
      </form>

      <div v-if="!tasks.length" class="p-8 text-center text-[#0240ab]">
        <h3>you don't have tasks</h3>
      </div>

      <ul v-else class="p-1.5 my-2 rounded-lg">
        <li v-for="(el, i) in tasks" :key="el.id" class="p-3 w-full flex justify-between">
          <strong>{{ i + 1 }}</strong>
          <p :class="{ 'line-through': el.isDone }">{{ el.title }}</p>
          <!-- <p :class="[el.isDone ? 'isDone' : ''] ">{{ el.isDone }}</p> -->

          <button
            @click="deleting(el.id)"
            class="px-5 border-none rounded-md delete bg-red-500 text-white focus:bg-white focus:text-[#3d3d3d] focus:border focus:border-red-500"
          >
            delete
          </button>
          <button
            :disabled="el.isDone"
            @click="isDone(el.id)"
            class="px-5 border-none rounded-md done bg-[#005CAD] text-white focus:bg-white focus:text-[#3d3d3d] focus:border focus:border-[#005CAD]"
          >
            done
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>
