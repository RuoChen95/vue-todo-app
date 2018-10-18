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
                      <input type="checkbox" v-model="components.checked" @click="toggleCheckCondition(index)"/>
                      <p :class="{'checked': components.checked}" v-if="editIndex != index" @dblclick="editComponents(index)">{{components.value}}</p>
                  </div>
                  <div class="buttons" v-if="editIndex != index">
                      <div class="delete" @click="deleteSection(index)">x</div>
                  </div>
                  <input class="edit" v-model="components.value" v-if="editIndex == index" v-todo-focus="editIndex == index" @blur="doneEdit(index)" @keyup.enter="doneEdit(index)" @keyup.esc="cancelEdit(index)">
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

      <footer class="info">
        <p>Double-click to edit a todo</p>
      <p>Prototype: <a href="http://todomvc.com">TodoMVC</a></p>
      </footer>
  </div>
</template>

<script>
// useless but is a mind tools
//var filters = {
//  all: function(todos) {
//    return todos;
//  },
//  active: function(todos) {
//    return todos.filter(function(todo) {
//      return !todo.completed;
//    });
//  },
//  completed: function(todos) {
//    return todos.filter(function(todo) {
//      return todo.completed;
//    });
//  },
//};
export default {
  name: 'list',
  props: {},
  data() {
    return {
      data: '',
      list: [],
      condition: 'all',
      editCache: '',
      editIndex: -1,
    };
  },
  mounted() {
    this.list = JSON.parse(localStorage.getItem('vue-todo')) || [];
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
    //    filterTodos: function() {
    //      return filters[this.condition](this.list);
    //    },
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
        element.checked = !element.checked;
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
    editComponents(index) {
      this.editCache = this.list[index].value;
      this.editIndex = index;
    },
    doneEdit() {
      this.editCache = '';
      this.editIndex = -1;
    },
    cancelEdit(index) {
      this.list[index].value = this.editCache;
      this.editIndex = -1;
    },
  },
  // a custom directive to wait for the DOM to be updated
  // before focusing on the input field.
  // http://vuejs.org/guide/custom-directive.html
  directives: {
    'todo-focus': function(el) {
      el.focus();
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
      padding: 0;
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
        position: relative;
        align-items: center;

          padding-left: 10px;
        input[type='checkbox'] {
          text-align: center;
          width: 40px;
          height: 40px;
          position: absolute;
          top: 0;
          bottom: 0;
          margin: auto 0;
          border: none;
          -webkit-appearance: none;
          opacity: 0;
        }
        p.checked {
          text-decoration: line-through;

          background-image: url('data:image/svg+xml;utf8,<svg%20xmlns%3D"http%3A//www.w3.org/2000/svg"%20width%3D"40"%20height%3D"40"%20viewBox%3D"-10%20-18%20100%20135"><circle%20cx%3D"50"%20cy%3D"50"%20r%3D"50"%20fill%3D"none"%20stroke%3D"%23bddad5"%20stroke-width%3D"3"/><path%20fill%3D"%235dc2af"%20d%3D"M72%2025L42%2071%2027%2056l-4%204%2020%2020%2034-52z"/></svg>');
        }
        p {
          font-size: 24px;
          word-break: break-all;
          color: #777;
          font-weight: 100;
            line-height: 2;
          width: 460px;
            margin-top: 10px;
            margin-bottom: 10px;

          background-image: url('data:image/svg+xml;utf8,<svg%20xmlns%3D"http%3A//www.w3.org/2000/svg"%20width%3D"40"%20height%3D"40"%20viewBox%3D"-10%20-18%20100%20135"><circle%20cx%3D"50"%20cy%3D"50"%20r%3D"50"%20fill%3D"none"%20stroke%3D"%23ededed"%20stroke-width%3D"3"/></svg>');
          background-repeat: no-repeat;
          background-position: center left;

          padding-left: 60px;

          transition: all 0.4s;
        }
      }
      div.buttons {
        display: none;
        padding-right: 15px;
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
      input.edit {
        width: 495px;
        height: 66px;
        outline: 0;
        font-size: 24px;
        color: #777;
        font-weight: 100;

        border: 1px solid #999;
        box-shadow: inset 0 -1px 5px 0 rgba(0, 0, 0, 0.2);
      }
      input.edit::placeholder {
        color: #f5f5f5;
        font-weight: 100;
        font-size: 24px;
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

footer.info {
    margin: 65px auto 0;
    color: #bfbfbf;
    font-size: 10px;
    text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5);
    text-align: center;
    a {
        color: inherit;
        text-decoration: none;
        font-weight: 400;
    }
}
</style>
<style lang="less" type="text/less">
body {
  background-color: #f5f5f5;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
</style>
