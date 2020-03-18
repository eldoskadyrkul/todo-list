<template>
  <div class="todo-list">
    <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo">
    <input type="text" class="todo-input" placeholder="Input your description" v-model="TodoDescription">
    <button class="submit_btn" @click="addTodo">Add Todo</button>

    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
      <div  class="divTable blueTable"  v-for="(todo, index) in sort(sortOrder)" :key="todo.id">
          <div class="divTableHeading">
          </div>
          <div class="divTableBody">
                <div class="divTableRow">
                  <input type="checkbox" v-model="todo.completed">
                  <div class="divTableCell">{{ todo.id }}</div>
                  <div class="divTableCell">{{ todo.title }}</div>
                  <div class="divTableCell">{{ todo.description }}</div>
                  <div class="divTableCell">
                    <button @click="removeTodo(index)" class="remove">X</button>
                  </div>
                </div>
          </div>
      </div>
    </transition-group>

    <div class="extra-container">
      <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All</label></div>
      <div>{{ remaining }} items left</div>
    </div>

    <div class="extra-container">
      <div>        
        <button v-for="sortkey in Object.keys(orderedListOptions)" v-on:click="sortOrder = sortkey" v-bind:class="{active: sortOrder == sortkey}">{{sortkey}}</button>
      </div>

      <div>
        <transition name="fade">
        <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data () {
    return {
      sortOrder: 'A-Z',
      newTodo: '',
      TodoDescription: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Finish Vue Screencast',
          'description': 'Vue',
          'completed': false,
          'editing': false,
        },
        {
          'id': 2,
          'title': 'Take over world',
          'description': 'Vue',
          'completed': false,
          'editing': false,
        },
      ]
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining != 0
    },
    orderedListOptions: function(){
      return {
        "A-Z": () => { return this.todos.slice().sort() },
        "Z-A": () => { return this.todos.slice().sort().reverse()},
      }
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    sort: function(sortOrder){
      return this.orderedListOptions[sortOrder]()
    },
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        description: this.TodoDescription,
        completed: false,
        editing: false,
      })
      this.newTodo = ''
      this.idForTodo++
      this.TodoDescription = ''
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit(todo) {
      if (todo.title.trim() == '') {
        todo.title = this.beforeEditCache
      }
      todo.editing = false
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    checkAllTodos() {
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>


<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
  .todo-input {
    width: 50%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    &:focus {
      outline: 0;
    }
  }
  .submit_btn {
    width: 100%;
    background: #68b768;
    border: none;
    padding: 15px 18px;
    margin: 10px 0;
    color: #fff;
    text-transform: uppercase;
    cursor: pointer;

    &:hover {
      color: #000;
    }
  }

  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }
  .remove-item {
    cursor: pointer;
    margin-left: 14px;
    &:hover {
      color: black;
    }
  }
  .todo-item-left { 
    display: flex;
    align-items: center;
  }
  div.blueTable {
    background-color: #71A9EE;
    width: 100%;
    text-align: left;
    border-collapse: collapse;
  }
  .divTable.blueTable .divTableCell, .divTable.blueTable .divTableHead {
    padding: 6px 4px;
  }
  .divTable.blueTable .divTableBody .divTableCell {
    font-size: 13px;
    text-align: center;
  }
  .divTable.blueTable .divTableRow:nth-child(even) {
    background: #D0E4F5;
  }
  .divTable.blueTable .divTableHeading {
    background: #1C6EA4;
    background: -moz-linear-gradient(top, #5592bb 0%, #327cad 66%, #1C6EA4 100%);
    background: -webkit-linear-gradient(top, #5592bb 0%, #327cad 66%, #1C6EA4 100%);
    background: linear-gradient(to bottom, #5592bb 0%, #327cad 66%, #1C6EA4 100%);
    border-bottom: 2px solid #444444;
  }
  .divTable.blueTable .divTableHeading .divTableHead {
    font-size: 15px;
    font-weight: bold;
    color: #FFFFFF;
    border-left: 2px solid #D0E4F5;
  }
  .divTable.blueTable .divTableHeading .divTableHead:first-child {
    border-left: none;
  }

  .blueTable .tableFootStyle {
    font-size: 14px;
    font-weight: bold;
    color: #FFFFFF;
    background: #D0E4F5;
    background: -moz-linear-gradient(top, #dcebf7 0%, #d4e6f6 66%, #D0E4F5 100%);
    background: -webkit-linear-gradient(top, #dcebf7 0%, #d4e6f6 66%, #D0E4F5 100%);
    background: linear-gradient(to bottom, #dcebf7 0%, #d4e6f6 66%, #D0E4F5 100%);
    border-top: 2px solid #444444;
  }
  .blueTable .tableFootStyle {
    font-size: 14px;
  }
  .blueTable .tableFootStyle .links {
     text-align: right;
  }
  .blueTable .tableFootStyle .links a{
    display: inline-block;
    background: #1C6EA4;
    color: #FFFFFF;
    padding: 2px 8px;
    border-radius: 5px;
  }
  .blueTable.outerTableFooter {
    border-top: none;
  }
  .blueTable.outerTableFooter .tableFootStyle {
    padding: 3px 5px; 
  }
  .divTable{ display: table; }
  .divTableRow { display: table-row; }
  .divTableHeading { display: table-header-group;}
  .divTableCell, .divTableHead { display: table-cell;}
  .divTableHeading { display: table-header-group;}
  .divTableFoot { display: table-footer-group;}
  .divTableBody { display: table-row-group;}
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }
  .remove {
    border: none;
    background: red;
    color: #fff;
  }
  .todo-item-label-desc {
    padding: 10px;
    font-size: 12px;
  }
  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc; //override defaults
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    &:focus {
      outline: none;
    }
  }
  .completed {
    text-decoration: line-through;
    color: grey;
  }
  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    &:hover {
      background: lightgreen;
    }
    &:focus {
      outline: none;
    }
  }
  .active {
    background: lightgreen;
  }
  // CSS Transitions
  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
