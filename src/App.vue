<template>
  <div class="app">
    <section class="greeting">
      <h1>
        What's up
        <input type="text" placeholder="name here" v-model.trim.lazy="name" />
      </h1>
      {{ name }}
    </section>

    <section class="create_todo">
      <h2>CREATE A TODO</h2>
      <form id="new-todo-form" @:submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          class="todo_input"
          placeholder="할 일을 입력해 주세요."
          v-model.trim.lazy="input_content"
        />
        <!-- 입력 내용 - {{ input_content }} -->
        <div class="options">
          <label class="label">
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label class="label">
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <!-- 선택한 카테고리 - {{ input_category }} -->
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo_list">
      <h3>TODO LIST</h3>

      todo_list - {{ todos }}
      <br />
      <br />
      todos_asc = {{ todos_asc }}

      <div class="list">
        <div
          :class="`todo_item ${todo.done && 'done'}`"
          v-for="(todo, index) in todos"
          :key="index"
        >
          <label for="">
            <input type="checkbox" />
            <span class="bubble personal"></span>
          </label>
          <div class="todo_content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @:click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { computed, onMounted, ref, watch } from "vue";

const name = ref("");
const input_content = ref(null);
const input_category = ref("");
const todos = ref([]);

const removeTodo = (item) => {
  todos.value = todos.value.filter((aa) => aa !== item);
};

// 시간순으로 정리(최신이 맨 위로 올라오게)
const todos_asc = computed(() =>
  todos.value.sort((a, b) => b.createAt - a.createAt)
);

// 할 일 입력값들을을 받아오는 함수
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  // 입력값이 없거나 카테고리를 선택하지 않았을 땐 리턴처리

  // console.log("할 일 입력값을 받아오는 함수 실행");

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
  });

  // 입력 후 초기화
  input_content.value = "";
  input_category.value = null;
};

watch(
  todos,
  (newVal) => {
    console.log("투두스 감지", todos);
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);
// deep - 속성 프로퍼티나, 하위 뎁스도 감지

// 이름 입력하는 것을 감지하고 로컬스토리지에 저장
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// 새로 열었을 때 로컬스로리지에서 불러오기
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>

<style lang="scss" scoped></style>
