<template>
	<div>
		<div class="contenair-list">
			<div class="form-group">
				<label class="col-form-label" for="inputDefault"><h2>To do list</h2></label>
				<input type="text" class="form-control" placeholder="Enter what needs to be done and type [enter]" v-model="newTodo" @keyup.enter="addTodo">
			</div>
			<transition-group name="fade" enter-active-class="animated bounceInDown" leave-active-class="animated bounceOutUp">
				<div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item p-0">
					<div class="col-lg-12 float-left item">
						<div class="checkbox">
							<div class="todo-item-left ">
								<label class="float-left" style="font-size: 1em">
									<input type="checkbox" v-model="todo.completed">
									<span class="mt-3 cr"><i class="cr-icon fa fa-check"></i></span>
								</label>
								<div v-if="!todo.editing" @dblclick="editTodo(todo)" class="float-left title-item item-label" :class="{ completed : todo.completed}">{{ todo.title}}</div>
								<input v-else type="text" class="float-left title-item todo-item-edit" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus/>
							</div>
						</div>	
						<div class="remove-item float-right">
							<i class="fa fa-times-circle-o" @click="removeTodo(index)"></i>
						</div>
					</div>
				</div>
			</transition-group>
		</div>
		<div class="col-lg-12 float-left border-bottom border-grey ">
			<div class="checkbox float-left">
				<label style="font-size: 1em" >
					<input type="checkbox"  :checked="!anyRemaining" @change="checkAllTodos">
					<span class="cr"><i class="cr-icon fa fa-check"></i></span>
					<strong> Check All</strong>
				</label>
			</div>
			<div class="float-right">{{ remaining }} <strong>items left</strong></div>
		</div>
		<div class="ccol-lg-12 float-left mt-3 ">
			<button type="button" class="btn mr-2 btn-sm btn-info" :class="{active: filter == 'all'}" @click="filter = 'all'"><strong> All </strong></button>
			<button type="button" class="btn mr-2 btn-sm btn-info" :class="{active: filter=='active'}" @click="filter = 'active'"><strong>Active</strong></button>
			<button type="button" class="btn mr-2 btn-sm btn-info" :class="{active: filter == 'completed'}" @click="filter = 'completed'"><strong> Completed </strong></button>
		</div>
		<transition enter-active-class="animated zoomIn">
			<div class="ccol-lg-12 float-right mt-3">
				<button type="button" class="btn mr-2 btn-sm btn-danger" v-if="showClearCompletedbutton"  @click="clearCompleted"><i class="fa fa-times"></i><strong> Delete completed </strong></button>
			</div>
		</transition>
	</div>
</template>

<script>
export default {
	name: 'todo-list',
		data(){
			return{
			newTodo: '',
			idForTodo: 0,
			beforeEditCache: '',
			filter: 'all',
			todos:[
				
			]
		}
	},
	computed:{
		remaining(){
			return this.todos.filter(todo => !todo.completed).length
		},
		anyRemaining(){
			return  this.remaining != 0
		},
		todosFiltered(){
			if (this.filter == 'all') {
				return this.todos
			}else if (this.filter == 'active') {
				return this.todos.filter(todo => !todo.completed)
			}else if (this.filter == 'completed') {
				return this.todos.filter(todo => todo.completed)
			}
			
			return this.todos
		},
		showClearCompletedbutton(){
			return this.todos.filter(todo => todo.completed).length > 0
		}
	},
	directives: {
		focus: {
			inserted: function(el){
				el.focus()
			}
		}
	},
	methods:{
		addTodo(){ 
			if(this.newTodo.trim().length == 0){
				return
			}
			this.todos.push({
				id:this.idForTodo,
				title:this.newTodo,
				completed:false,
				editing: false,
			})
			this.newTodo = '',
			this.idForTodo +=1
		},
		editTodo(todo){
			this.beforeEditCache = todo.title
			todo.editing = true
		},
		doneEdit(todo){
			if(todo.title.trim() == ''){
				todo.title = this.beforeEditCache
			}
			todo.editing = false
			
		},
		cancelEdit(todo){
			todo.title = this.beforeEditCache
			todo.editing = false
		},
		removeTodo(index){
			this.todos.splice(index, 1)
		},
		checkAllTodos(){
			this.todos.forEach((todo) => todo.completed = event.target.checked)
		},
		clearCompleted(){
			this.todos = this.todos.filter(todo => !todo.completed)
		}
	}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
 @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css");
.contenair-list{
	min-height: 400px;
	max-width: auto;
	float: left;
	width: 100%;
	padding: 0;
	margin-bottom: 20px;
}
.form-control{
	padding: 12px;
}
.item{
	height: auto;
	padding: 0 13px 0px 13px ;
	border-bottom: 1px solid #e7eaee;
	transition: 5s;
	background-color: #f8fafc;
	margin-bottom: 2px;
	border-radius: 3px;
	cursor: pointer;
	transition: 0.2s;

}
.item:hover{
	background-color: #616b74;
	color: white;
}
.title-item{
	padding: 13px 0 1px 3px;
}
.remove-item{
	color: #dadada;
	font-size: 33px;
	padding-top: 0px;
}
.remove-item:hover{
	color: #9a3a3a;

}
.todo-item-edit{
	padding: 5px 5px 5px 1px;
	margin-top: 6px;
	width: 88%;
}
.item-checkbox{
	margin: 16px 7px 0 10px;
}
.completed{
	text-decoration: line-through;
	color: #999999;;
}
 .item:hover .completed{
	color: white;

}
.checkbox label:after, 
.radio label:after {
    content: '';
    display: table;
    clear: both;
}

.checkbox .cr,
.radio .cr {
    position: relative;
    display: inline-block;
    border: 1px solid #a9a9a9;
    border-radius: .25em;
    width: 1.3em;
    height: 1.3em;
    float: left;
    margin-right: .5em;
}

.radio .cr {
    border-radius: 50%;
}

.checkbox .cr .cr-icon,
.radio .cr .cr-icon {
    position: absolute;
    font-size: .8em;
    line-height: 0;
    top: 50%;
    left: 20%;
}

.radio .cr .cr-icon {
    margin-left: 0.04em;
}

.checkbox label input[type="checkbox"],
.radio label input[type="radio"] {
    display: none;
}

.checkbox label input[type="checkbox"] + .cr > .cr-icon,
.radio label input[type="radio"] + .cr > .cr-icon {
    transform: scale(3) rotateZ(-20deg);
    opacity: 0;
    transition: all .3s ease-in;
}

.checkbox label input[type="checkbox"]:checked + .cr > .cr-icon,
.radio label input[type="radio"]:checked + .cr > .cr-icon {
    transform: scale(1) rotateZ(0deg);
    opacity: 1;
}

.checkbox label input[type="checkbox"]:disabled + .cr,
.radio label input[type="radio"]:disabled + .cr {
    opacity: .5;
}
</style>
