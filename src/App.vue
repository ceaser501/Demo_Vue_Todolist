<template>
  <v-app>
    <v-main>
      <v-container class="col-6">
        <!-- HEADER -->
        <v-row class="text-center">
          <v-col>
            <h1 class="font-weight-bold font-italic">TODO LIST!</h1>
          </v-col>
        </v-row>

        <!-- ADD -->
        <v-row>
          <v-col>
            <todo-add @add-text="addText"></todo-add>
          </v-col>
        </v-row>

        <!-- LIST -->
        <v-row>
          <v-col>
            <v-card>
              <v-list>
                <div v-if="todoItems.length == 0">
                  <v-list-item>
                    <v-list-item-content
                      >데이터가 없습니다.</v-list-item-content
                    >
                  </v-list-item>
                </div>
                <div v-else>
                  <todo-list
                    v-for="(item, index) in filteredTodoItems"
                    :todo-item.sync="filteredTodoItems[index]"
                    :key="filteredTodoItems[index].id"
                    @remove-todo="removeTodo"
                  ></todo-list>
                </div>
              </v-list>
            </v-card>
          </v-col>
        </v-row>

        <!-- FOOTER -->
        <v-row>
          <v-col>
            <todo-footer
              @remove-all="clearAll"
              @remove-comp="clearComp"
              @show-todo-items="showTodoItems"
            >
            </todo-footer>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
/** 컴포넌트 IMPORT **/
import TodoAdd from "./components/TodoAdd";
import TodoList from "./components/TodoList";
import TodoFooter from "./components/TodoFooter";

const LOC_STRG_KEY = "todos";

export default {
  data() {
    return {
      todoItems: [],
      filteredType: ""
    };
  },
  created() {
    this.filteredType = 0; // 초기 값은 전체조회
    this.todoItems = JSON.parse(localStorage.getItem(LOC_STRG_KEY)) || [];
  },
  watch: {
    todoItems: function() {
      localStorage.setItem("todos", JSON.stringify(this.todoItems));
    }
  },
  computed: {
    filteredTodoItems: function() {
      switch (this.filteredType) {
        case 0:
          return this.todoItems;
        case 1:
          return this.todoItems.filter(item => item.completed);
        case 2:
          return this.todoItems.filter(item => !item.completed);
        default:
          return [];
      }
    }
  },
  methods: {
    /** 메서드1 : 할일 추가 **/
    addText(value) {
      this.todoItems = [
        ...this.todoItems,
        { id: new Date().getTime(), value: value, completed: false }
      ];
    },

    /** 메서드2 : 건별삭제 **/
    removeTodo(todoItemId) {
      this.todoItems = this.todoItems.filter(item => item.id != todoItemId);
    },

    /** 메서드3 : 전체삭제 **/
    clearAll() {
      this.todoItems = [];
    },

    /** 메서드4 : 완료항목 삭제 **/
    clearComp() {
      this.todoItems = this.todoItems.filter(item => !item.completed);
    },

    /** 메서드5 : 전체/완료/미완료 항목보기 **/
    showTodoItems(type) {
      this.filteredType = type;
    }
  },
  components: {
    TodoAdd: TodoAdd,
    TodoList: TodoList,
    TodoFooter: TodoFooter
  }
};
</script>
