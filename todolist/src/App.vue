<template>
  <div class="add">
    <p>Добавить задачу</p>
    <input type="text" placeholder="Задача" v-model="new_todo"> 
    <input type="radio" v-model="active" id="status">
    <label>Активная</label>
    <button class="button" type="submit" @click="add_todo()">Добавить</button>
  </div>

  <div class="filters">
    <p>Фильтры</p>
    <button class="button" @click="show_act()" id="act">Активные</button>
    <button class="button" @click="show_inact()" id="inact">Неактивные</button>
    <button class="button" @click="show_all()" id="all">Все</button>
  </div>

  <div class="delete">
    <p>Удалить задачу</p>
    <input type="text" v-model="del">
    <button class="button" @click="del_todo()">Удалить</button>
  </div>

  <div v-if="flags.show_all">
    <!--Выводим все задачи-->
    <table id="customers" border="2">
      <caption>Задачи</caption>
      <tr>
        <th>Номер</th>
        <th>Задача</th>
        <th>Статус</th>
      </tr>
      <tr v-for="todo in todos" :key="todo">
        <td>{{ todo.id }}</td>
        <td>{{ todo.text }}</td>
        <td>{{ todo.active }}</td>
      </tr>
    </table>
  </div>

  <div v-if="flags.show_active">
    <!--Выводим только активные задачи-->
     <table id="customers" border="2">
      <caption>Задачи</caption>
      <tr>
        <th>Номер</th>
        <th>Задача</th>
        <th>Статус</th>
      </tr>
      <tr v-for="todo in todos_active" :key="todo">
        <td>{{ todo.id }}</td>
        <td>{{ todo.text }}</td>
        <td>{{ todo.active }}</td>
      </tr>
    </table>
  </div>

  <div v-if="flags.show_inactive">
    <!--Выводим только неактивные задачи-->
    <table id="customers" border="2">
      <caption>Задачи</caption>
      <tr>
        <th>Номер</th>
        <th>Задача</th>
        <th>Статус</th>
      </tr>
      <tr v-for="todo in todos_inactive" :key="todo">
        <td>{{ todo.id }}</td>
        <td>{{ todo.text }}</td>
        <td>{{ todo.active }}</td>
      </tr>
    </table>
  </div>  

</template>


<script>
import axios from 'axios'

export default {
  name: 'App',

  data(){
    return{
      todos: [],
      todos_active: [],
      todos_inactive: [],
      err: '',
      new_todo: '',
      active: false,
      del: 0,
      flags:{
        show_all: false,
        show_active: false,
        show_inactive: false
      }
    }
  },

  created(){
    //получаем GET-запросом задачи из хранилища и устанавливаем флаг на показ всех задач
    axios
      .get('https://my-json-server.typicode.com/falk20/demo/todos')
      .then(response => {this.todos = response.data})
      .catch(error => {this.err = error.response})
    this.flags.show_all = true;
  },

  methods:{
    add_todo(){
      //добавление собственной задачи с отметкой активности
      var status = document.getElementById("status")
      if(status.checked == true){
        this.active = true;
      }else{
        this.active = false;
      }
      var obj = {
        id: this.todos[this.todos.length - 1].id + 1,
        text: this.new_todo,
        active: this.active
      }
      this.todos.push(obj)
    },

    show_act(){
      //отображение только активных задач
      this.todos_active.length = 0;
      this.flags.show_active = true;
      this.flags.show_inactive = false;
      this.flags.show_all = false;
      var obj = this.todos;
      var obj_active = this.todos_active;
      var button1 = document.getElementById("act");
      var button2 = document.getElementById("inact");
      var button3 = document.getElementById("all");
      button1.disabled = true;
      button2.disabled = false;
      button3.disabled = false;
      //идем в цикле по массиву с задачами и во вспомогательный массив записываем те задачи, у которых стоит статус "активна"
      obj.forEach(function(item, i, obj){
        if(obj[i].active == true){
          obj_active.push(obj[i])
        }
      })
    },

    show_inact(){
      //отображение только неактивных задач
      this.todos_inactive.length = 0;
      this.flags.show_active = false;
      this.flags.show_inactive = true;
      this.flags.show_all = false;
      var obj = this.todos;
      var obj_inactive = this.todos_inactive;
      var button1 = document.getElementById("act");
      var button2 = document.getElementById("inact");
      var button3 = document.getElementById("all");
      button1.disabled = false;
      button2.disabled = true;
      button3.disabled = false;
      //идем в цикле по массиву с задачами и во вспомогательный массив записываем те задачи, у которых стоит статус "неактивна"
      obj.forEach(function(item, i, obj){
        if(obj[i].active == false){
          obj_inactive.push(obj[i])
        }
      })
    },

    show_all(){
      //отображение всех задач
      this.flags.show_active = false;
      this.flags.show_inactive = false;
      this.flags.show_all = true;
      var button1 = document.getElementById("act");
      var button2 = document.getElementById("inact");
      var button3 = document.getElementById("all");
      button1.disabled = false;
      button2.disabled = false;
      button3.disabled = true;
    },

    del_todo(){
      //удаление задачи по ее номеру
      this.del = Number(this.del); //переводим строку в число
      var obj = this.todos;
      var del = this.del;
      //проходим по массиву с задачами и ищем элемент с нужным id, потом удаляем его
      obj.forEach(function(item, i, obj){
        if(obj[i].id == del)
          obj.splice(i, 1)
      })
      } 
    },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#customers {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#customers td, #customers th {
  border: 1px solid #ddd;
  padding: 8px;
}

#customers tr:nth-child(even){background-color: #f2f2f2;}

#customers tr:hover {background-color: #ddd;}

#customers th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #04AA6D;
  color: white;
}

.button {
  background-color: #4CAF50; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}
</style>
