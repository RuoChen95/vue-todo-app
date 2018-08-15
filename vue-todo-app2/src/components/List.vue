<template>
  <div class="list">
      <h1 class="title">todos</h1>
      <div class="todoapp">
          <div class="input">
              <div @click="completeAll()" class="completeAll">Complete All</div>
              <input @keyup.enter="add()" v-model="data" placeholder="What needs to be done?"/>
          </div>
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
                      <div class="delete" @click="deleteSection(index)">x</div>
                  </div>
              </div>
          </div>
          <div class="banner">
              <p class="left">{{leftAmount}} {{pluralize('item', leftAmount)}} left</p>
              <div class="condition">
                  <div @click="condition = 'all'" :class="{'active': this.condition == 'all'}">All</div>
                  <div @click="condition = 'active'" :class="{'active': this.condition == 'active'}">Active</div>
                  <div @click="condition = 'completed'" :class="{'active': this.condition == 'completed'}">Completed</div>
              </div>
              <div @click="clear()" :class="{'show': hasCompleted}" class="clearCompleted">Clear Completed</div>
          </div>
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
  width: 550px;
  margin: 0 auto;
  h1.title {
    margin: 0 auto;
    font-size: 100px;
    text-align: center;
    font-weight: 100;
    color: rgba(175, 47, 47, 0.15);
  }
  div.todoapp {
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
    background: white;
  }
  div.input {
    display: flex;
    height: 33px;
    padding: 16px 0;
    background: white;
    box-shadow: inset 0 -2px 0 #ededed;
    div.completeAll {
      flex-shrink: 0;
      width: 60px;
      padding: 0 5px;
      font-size: 13px;
      color: #777;
      font-weight: 100;
      text-align: center;
      cursor: pointer;
    }
    input {
      width: 100%;
      border-width: 0;
      outline: 0;
      font-size: 24px;
      color: #777;
      font-weight: 100;
    }
    input::placeholder {
      color: #f5f5f5;
      font-weight: 100;
      font-size: 24px;
    }
  }
  div {
    div.section {
      display: none;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid #ededed;
      padding: 0 15px 0 0;
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
        div.condition {
          text-align: center;
          width: 70px;
          input[type='checkbox'] {
          }
        }
        p.checked {
          text-decoration: line-through;
        }
        p {
          font-size: 24px;
          word-break: break-all;
          color: #777;
          font-weight: 100;
        }
      }
      div.buttons {
        display: none;
        div.delete {
          font-size: 20px;
          color: #777;
          font-weight: 100;
          cursor: pointer;
          &:hover {
            color: rosybrown;
          }
        }
      }
    }
  }
  div.banner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 15px;
    height: 50px;
    position: relative;
    color: #777;
    font-weight: 100;
    &:before {
      content: '';
      position: absolute;
      right: 0;
      bottom: 0;
      left: 0;
      height: 50px;
      overflow: hidden;
      box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6, 0 9px 1px -3px rgba(0, 0, 0, 0.2),
        0 16px 0 -6px #f6f6f6, 0 17px 2px -6px rgba(0, 0, 0, 0.2);
      z-index: -1;
    }
    p.left {
      width: 100px;
    }
    div.condition {
      display: flex;
      > div {
        padding: 2px 10px;
        margin: 0 5px;
        cursor: pointer;
        &:hover {
          box-shadow: 0 0 0 1px rgba(175, 47, 47, 0.1);
        }
      }
      > div.active {
        box-shadow: 0 0 0 1px rosybrown;
        border-radius: 3px;
      }
    }
    div.clearCompleted {
      visibility: hidden;
      font-size: 13px;
      color: #777;
      width: 100px;
      &.show {
        visibility: visible;
      }
      &:hover {
        text-decoration: underline;
        cursor: pointer;
      }
    }
  }
}
</style>
<style lang="less" type="text/less">
body {
  background-color: #f5f5f5;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
</style>
