<template>
  <div class="list">
      <button @click="completeAll()">Complete All</button>
      <input @keyup.enter="add()" v-model="data"/>
      <button @click="add()">add</button>
      <div>
          <div v-for="(components, index) in list" :key="index" class="section" :class="{'show': components.show}">
              <div class="content">
                  <!--checked; priority-->
                  <div class="condition">
                      <input type="checkbox" v-model="components.checked" @click="toggleCheckCondition(index)"/>
                  </div>
                  <p :class="{'checked': components.checked}">{{components.value}}</p>
              </div>
              <div class="buttons">
                  <button class="delete" @click="deleteSection(index)">delete</button>
              </div>
          </div>
      </div>
      <div class="banner">
          <p>{{leftAmount}} {{pluralize('item', leftAmount)}} left</p>
          <div class="condition">
              <div @click="condition = 'all'" :class="{'active': this.condition == 'all'}">All</div>
              <div @click="condition = 'active'" :class="{'active': this.condition == 'active'}">Active</div>
              <div @click="condition = 'completed'" :class="{'active': this.condition == 'completed'}">Completed</div>
          </div>
          <button @click="clear()" :class="{'show': hasCompleted}" class="clearCompleted">Clear Completed</button>
      </div>
  </div>
</template>

<script>
// useless but is a mind tools
var filters = {
  all: function(todos) {
    return todos;
  },
  active: function(todos) {
    return todos.filter(function(todo) {
      return !todo.completed;
    });
  },
  completed: function(todos) {
    return todos.filter(function(todo) {
      return todo.completed;
    });
  },
};
export default {
  name: 'list',
  props: {},
  data() {
    return {
      data: '',
      list: JSON.parse(localStorage.getItem('vue-todo')) || [],
      condition: 'all',
    };
  },
  watch: {
    list: {
      deep: true,
      handler: function(val) {
        localStorage.setItem('vue-todo', JSON.stringify(val));
      },
    },
    condition: function(val) {
      if (val == 'all') {
        this.list.forEach(function(element) {
          element.show = true;
        });
      } else if (val == 'active') {
        this.list.forEach(function(element) {
          if (element.checked == true) {
            element.show = false;
          } else {
            element.show = true;
          }
        });
      } else if (val == 'completed') {
        this.list.forEach(function(element) {
          if (element.checked == true) {
            element.show = true;
          } else {
            element.show = false;
          }
        });
      }
    },
  },
  computed: {
    leftAmount: function() {
      let total = 0;
      this.list.forEach(function(element) {
        if (element.checked == false) {
          total = total + 1;
        }
      });
      return total;
    },
    hasCompleted: function() {
      let length = 0;
      this.list.forEach(function(element) {
        if (element.checked != true) {
          length++;
        }
      });
      if (length == this.list.length) {
        return false;
      } else {
        return true;
      }
    },
    // useless but is a mind tools
    filterTodos: function() {
      return filters[this.condition](this.list);
    },
  },
  methods: {
    pluralize: function(word, count) {
      return word + (count === 1 || count === 0 ? '' : 's');
    },
    add: function() {
      this.list.push({ value: this.data, checked: false, show: true });
      this.data = '';
    },
    deleteSection: function(i) {
      this.list.splice(i, 1);
    },
    toggleCheckCondition: function(i) {
      this.list[i].checked = !this.list[i].checked;
    },
    completeAll() {
      this.list.forEach(function(element) {
        element.checked = true;
      });
    },
    clear() {
      let arr = [];
      this.list.forEach(function(element) {
        if (element.checked == false) {
          arr.push(element);
        }
      });
      this.list = arr;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less" type="text/less">
div.list {
  div {
    div.section {
      display: none;
      justify-content: space-between;
      align-items: center;
      border: 1px solid rosybrown;
      &.show {
        display: flex;
      }
      &:hover {
        div.buttons {
          display: flex;
        }
      }
      div.content {
        display: flex;
        align-items: center;
        p.checked {
          text-decoration: line-through;
        }
      }
      div.buttons {
        display: none;
      }
    }
  }
  div.banner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    div.condition {
      display: flex;
      > div.active {
        border: 1px solid rosybrown;
        border-radius: 3px;
      }
    }
    button.clearCompleted {
      visibility: hidden;
      &.show {
        visibility: visible;
      }
    }
  }
}
</style>
