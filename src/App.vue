<template lang="pug">
  div#app.container.mt-3
    .card
      .card-body
        .row
          .col-sm
            .input-group.mb-3
              input(type="text" placeholder="Enter Task" ref="input_task" v-model="task" v-on:keyup.enter="submitTask").form-control
        .row.p-3
          h1 To Do List
        .row
          .col-sm
            .blackquote.text-center(v-if="this.tasks.length == 0")
              p.mb-o Seems like you are happy right now.. no tasks yet?
            TaskList(:tasks="this.tasks" @messageFromChild="childMethod")
</template>

<script>
import TaskList from './components/TaskList'
import axios from 'axios';
export default {
  name: 'App',
  components: {
    TaskList,
  },
  data () {
    return {
      tasks:[],
      task:'',
      api_token: 'TVdqWWRiaUdhZmNZVlJjSjF0QXI1NWtPQmd3bmNhS2RyUGYwZENCZA==',
      isEditing: false,
      task_id: 0
    }
  },
  mounted () {
    this.getTasks()
  },
  updated(){
    this.$refs.input_task.focus();
  },
  methods:{
    childMethod(method,task_id,task){
      this.task_id = task_id;
      switch(method){
        case 'edit':
          this.editTask(task);
          this.isEditing = true;
          break;
        case 'done':
          this.markDone();
          break;
        case 'delete':
          this.deleteTask();
          break;
        default:
          this.getTasks();
          break;
      }
    },
    getTasks(){
      axios
        .get('http://itmaskinen-todo-list.test/api/v1/tasks',{
          headers: {
            'x-api-token': this.api_token, 
            'user-id':1
          }
        })
        .then(response => (this.tasks = response.data))
    },
    submitTask(){
      if(this.isEditing)
        this.updateTask()
      else
        this.createTask()
    },
    editTask(task){
      this.task = task
      this.$refs.input_task.focus();
    },
    createTask(){
      //implement create task
    },
    updateTask(){
      axios
      .patch('http://itmaskinen-todo-list.test/api/v1/tasks/'+this.task_id,{
        "task":this.task
        },{
        headers: {
          'x-api-token': this.api_token, 
          'user-id':1
        }
      })
      .then(this.getTasks)
      this.task = ''
      this.isEditing = false
    },
    markDone(){
      let taskObj = this.findTaskById(this.task_id);
      let isDone = false;
      if(taskObj.done == 1)
        isDone = false;
      else
        isDone = true;
      axios
      .patch('http://itmaskinen-todo-list.test/api/v1/tasks/'+this.task_id,{
        "done":isDone
        },{
        headers: {
          'x-api-token': this.api_token, 
          'user-id':1
        }
      })
      .then(this.getTasks)
      this.isEditing = false;
    },
    deleteTask(){
      axios
      .delete('http://itmaskinen-todo-list.test/api/v1/tasks/'+this.task_id,{
        headers: {
          'x-api-token': this.api_token, 
          'user-id':1
        }
      })
      .then(this.getTasks)
      this.isEditing = false;
    },
    findTaskById(task_id){
      for(let i = 0; i < this.tasks.length; i++){
        if(this.tasks[i].id == task_id)
          return this.tasks[i];
      }
    }
  }
}
</script>

<style>
@import 'https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css';

</style>
