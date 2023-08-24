<template lang="">
   <div class="wrap">
        <h1>To-Do Page</h1>
        <div v-if="loading">
            Loading...
        </div>
        <form v-else @submit.Prevent="onSave">
            <div class="row">
                <div class="form-group">
                    <label>Todo Subject</label>
                    <input type="text" v-model="todo.subject">
                </div>
                <div class="form-group">
                    <label class="display">Status</label>
                    <div class="position">
                        <button @click="toggleTodoStatus" class="btn" type="button" :class="todo.completed ? 'btnG': 'btnR'">{{todo.completed ? '완료': '미완료'}}</button>
                    </div>
                </div>
            </div>
            <button @click="onSave" class="btn btnS" :class="[todoUpdated ? 'btnR' : '']" type="submit" :disabled="!todoUpdated">저장</button>
            <button class="btn" @click="moveToTodoListPage">취소</button>
        </form>
   </div>
</template>
<script>
import {useRoute, useRouter} from 'vue-router';
import axios from 'axios';
import {ref, computed} from 'vue';
import _ from 'lodash';
export default {
    setup(){
        const route=useRoute();
        const router=useRouter();
        const todo=ref(null);
        const loading=ref(true);
        const todoId=route.params.id;
        const originalTodo=ref(null);

        const getTodo= async () =>{
            const res= await axios.get(`http://localhost:3000/todos/${todoId}`)
            todo.value={...res.data}
            originalTodo.value={...res.data}
            loading.value= false;
        }
        const toggleTodoStatus = () =>{
            todo.value.completed=!todo.value.completed;
        };
        const moveToTodoListPage=()=>{
            router.push({
                name: "todos"
            })
        }
        const todoUpdated = computed(()=>{
            return !_.isEqual(todo.value, originalTodo.value)
        })
        const onSave=async () =>{
            const res=await axios.put(`http://localhost:3000/todos/${todoId}`, {
                subject: todo.value.subject,
                completed: todo.value.completed
            });
            originalTodo.value={...res.data}
        }

        getTodo();
        return {
            todo,
            loading,
            toggleTodoStatus,
            moveToTodoListPage,
            onSave,
            todoUpdated,
        }
    }
}
</script>
<style lang="scss">
    .wrap{
        width: 1000px;
        margin: 0 auto;
        h1{text-align: center;}
        >div{}
        >form{
            .row{
                .form-group{
                    position: relative;
                    display: flex;
                    flex-direction: column;
                    label{margin-bottom: 10px;}
                    .display{display: none;}
                    input{padding: 5px 10px; box-sizing: border-box; margin-bottom: 10px;}
                    .position{position: absolute; right: 10px; top: -45px;}
                }
            }
            .btn{border: none; margin: 10px 0;}
            .btnR{background: red; color: #fff; margin-right: 5px;}
            .btnG{background: green; color: #fff;}
        }
    }
    
</style>