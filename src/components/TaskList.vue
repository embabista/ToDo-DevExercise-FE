<template lang="pug">
    .col
        ul.list-group
            li.list-group-item.d-flex.justify-content-between.align-items-center(v-for="task in tasks") 
                p(v-if="task.done") 
                    del {{task.task}}
                p(v-else) {{task.task}} 
                .btn-group.btn-group-toggle
                    a.btn.btn-primary.btn-lg(href="#" role="button" @click="sendMessagetoParent('edit',task.id,task.task)")
                        font-awesome-icon(icon="edit")
                    a.btn.btn-success.btn-lg(href="#" role="button" @click="sendMessagetoParent('done',task.id,task.task)")
                        font-awesome-icon(icon="check-circle")
                    a.btn.btn-danger.btn-lg(href="#" role="button" @click="sendMessagetoParent('delete',task.id,task.task)")    
                        font-awesome-icon(icon="times-circle")
    
</template>
<script>
export default {
    name: 'TaskList',
    props:{
        tasks: Array
    },
    methods:{
        sendMessagetoParent(action,id,task){
            this.$emit('messageFromChild',action,id,task);
        }
    }
}
</script>
