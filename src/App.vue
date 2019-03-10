<template>
    <div id="app" class="container">
	  	<header>
		    <img class="logo" src="https://image.flaticon.com/icons/svg/148/148932.svg">
		 </header>
    		
    	<div>
	
		 <input v-model="new_todo" @keyup.enter="add_to_do" type="text" placeholder="What needs to be done" class="todo-input-text">

		<input type="submit" @click="add_to_do" value="Add to do" class="submit-btn">
	  	<transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
		<div v-for="(todo,index) in todos_filtered" :key="todo.id" class="todo-wrap"> 
			<div class="todo-checkbox-wrap"><input type="checkbox" v-model="todo.completed"></div> 
			<div class="remove-btn" @click="remove_todo(todo)"> &times; </div>	
				<div v-if="!todo.editing" @dblclick="editing(todo)" class="todo-item" :class="{completed : todo.completed }"> {{ todo.title }}</label> </div>
			<input type="text"  v-else class="edit-todo-input"  @blur="done_editing(todo)" @keyup.enter="done_editing(todo)" @keyup.esc="cancel_edit(todo)" v-model="todo.title" v-focus>
		</div>
		</transition-group>
		
		<footer class="box" v-if="todos.length">
				<label class="todo-checkbox-wrap"><input type="checkbox" @change="check_all" :checked="!remaining"> &nbsp;  Check All </label>
				<p> {{ remaining }} item<span v-if="active_todos().length > 1">s</span> remaining </p>
		</footer>
			<div class="box">
				<div>
					<transition name="fade">
					<button v-if="todos.length" :class="{active: filter == 'all'}" @click="filter = 'all'">All</button>
					</transition>
					<transition name="fade">
					<button v-if="active_todos().length" :class="{active: filter == 'active'}" @click="filter = 'active'">Active	</button>
					</transition>
					<transition name="fade">
					<button v-if="completed_todos().length" :class="{active: filter == 'completed'}" @click="filter = 'completed'" >Completed</button>
					</transition>
				</div>
				<div>
					<transition name="fade">
					<button class="clear_completed" @click="clear_completed" v-if="completed_todos().length">Clear Completed</button>
					</transition>
					</div>
			</div>

	</div>


   	</div>

</template>

<script>
	
export default {
		data(){
			return {
				new_todo: '',
				next_to_do: 0,
				before_edit: '',
				filter: 'all',
				todos: []
			}
		},
		directives: {
			focus: {
				inserted: function(el) {
					el.focus();
				}
			}
		},
		computed: {
			remaining() {
				return this.todos.filter(todo => !todo.completed).length
			},
			todos_filtered() {
				if (this.filter == 'active'){
					return this.active_todos();
				} else if (this.filter == 'completed') {
					return this.completed_todos();
				}

				return this.todos;
			}

		},
		watch: {
			todos: {
    	   		 handler(val){
          			localStorage.setItem('todos',JSON.stringify(val));
        		},
        		deep: true
      		}
		},
		mounted() {
				if (localSto.getItem('todos')) {
   					this.todos = JSON.parse(localStorage.getItem('todos'));
				} else {
					this.todos = [];
					localStorage.setItem('todos', JSON.parse(''));
				}
		},
		methods: {
			active_todos() {
				return this.todos.filter(todo => !todo.completed);
			},
			completed_todos() {
				return this.todos.filter(todo => todo.completed);
			},
			add_to_do() {

					if (this.new_todo.trim().length == 0 ) {
						alert("You shouldn't be using this app if you are really doing nothing.");
						return false;
					}



					this.todos.push({
						"id": this.next_to_do++,
						"title": this.new_todo,
						"completed": false,
						"editing": false
					})
				
					this.new_todo = '';
					this.next_to_do = this.next_to_do++;

			},
			editing(todo) {


					todo.editing = true;
				
					this.before_edit = todo.title;

					this.todos.index = false; 


			},
			done_editing(todo){
				
				if ( todo.title.trim() == '') {
					todo.title = this.before_edit;
				}
				
				todo.editing = false;
					
			},
			cancel_edit(todo) {
				

				todo.title = this.before_edit;
				todo.editing = false;
				
			},
			remove_todo(todo) {
				this.todos.splice(todo,1);
			},
			check_all(){
				this.todos.forEach( (todo) => todo.completed = event.target.checked );
			},
			clear_completed() {
				this.todos = this.todos.filter(todo => !todo.completed);
			}
		}

	}

</script>

<style type="text/css">
* {
	box-sizing: border-box;
}
html {
	height: 100%;

}
body {
	background-image: linear-gradient(120deg, #a1c4fd 0%, #c2e9fb 100%);
	background-attachment: fixed;
	font-size: 1rem;
	font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
}

#app {
  font-family: 'Segoe UI', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.container {
	max-width: 600px;
	margin: auto;
	background: #fff;
	padding: 1rem;
	border-radius: 10px;
	box-shadow: ;
}
header {
	text-align: center;
}
.logo {
	width: 80px;
	display: block;
	margin: auto;
}


@import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css');
.todo-wrap {
	border-bottom: 1px solid #ccc;
	padding: 1rem 0;
	margin-bottom: 1rem;
}
.todo-input-text {
	border: 1px solid #ccc;
	font-family: inherit;
	padding: 1rem;
	display: block;
	width: 100%;
	margin: 1rem 0;
	outline: 0;
}


.todo-checkbox-wrap {
	float: left;
	margin-right: 1rem;
	display: inline-block;
}
.todo-item {
	padding: .2em .6em;
	font-size: 1rem;
	display: flex;
	align-items: center;
	justify-content: space-between;
}
.edit-todo-input {
	padding: .2em .5em;
	display: inline-block;
	width: 80%;
	color: inherit;
	font-family: inherit;
	font-size: 1rem;
}

.todo-item > div {
	flex: 1 1 2 ;
	font-size: 1.1rem;
}

.remove-btn  {
	text-align: right;
	font-weight: bold;
	cursor: pointer;
	font-weight: bolder;
	float: right;
}

.remove-btn:hover {
	color: #c00;
}

.submit-btn {
	display: block;
	width: 100%;
	padding: 1rem 0;
	background: #fff;
	border: 1px solid #2980b9;
	transition: .4s ease-out background;
	border-radius: 3px;
	cursor: pointer;
	font-size: 1rem;
	font-family: inherit;
}
.submit-btn:hover {
	background: #2980b9;
	color: #fff;
}

.completed {
	color: #bbb;
	text-decoration: line-through;
	transition: .4s ease-out all;

}

.box {
	display: flex;
	align-items: center;
	justify-content: space-between;
	transition: .3s ease-out all;
}


.box button {
	border: 1px solid #ccc;
	padding: .2rem .5rem;
	cursor: pointer;
	display: inline-block;
	margin-right: .2rem;
	border-radius: 10px;
	font-size: .8rem;
	background: #fff;
	outline: 0;
	transition: .4s ease-out all;
}
.box .active,.clear_completed:hover {
	background: #27ae60;
	color: #fff;
	border: 1px solid #27ae60;
}
.fade-enter-active, .fade-leave-active {
	transition: opacity .2s;
}
.fade-enter, .fade-leave-to {
	opacity: 0;
}

@media(max-width: 700px) {
	.container {
		width: 98%;
	}
}
</style>