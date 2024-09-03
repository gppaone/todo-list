<script>
    import { onMount } from 'svelte';

    let todolist = [];

    onMount(() => {
        const savedTodos = localStorage.getItem('todolist');
        if (savedTodos) {
            todolist = JSON.parse(savedTodos);
        }
    });
    let newtodotext = '';
    let todotext = '';
    let todoitemupdate = '';
    let todocolor = '#ffffff';
    let todocolorupdate = '';

    $: if (todolist.length) { localStorage.setItem('todolist', JSON.stringify(todolist));}

    function addTodoItem(){
        todolist = [{text: newtodotext, done: false, color: todocolor}, ...todolist];
        todotext = '';
    }
    function editTodoItem(idx){
        todolist[idx].text = todoitemupdate;
        todolist[idx].color = todocolorupdate;
        todolist = [...todolist];
    }
    function loadEditTodo(idx){
        todoitemupdate = todolist[idx].text;
        todocolorupdate = todolist[idx].color;
    }

    function removeTodoItem(todo){
        todolist = todolist.filter((t) => t !== todo);
    }

</script>

<main>
    <form on:submit|preventDefault={() => addTodoItem()}>
        <div class="input-group">
            <input type="text" class="form-control" bind:value={newtodotext} placeholder="Add Todo" /><button type="submit" class="btn btn-success">Add</button>
        </div>
    </form>
    <div class="todolist d-flex list-group" id="todoaccordion">
        {#if todolist.length > 0}
        {#each todolist as todo, index}
            <div class="list-group-item todoitem" id={index} style="background-color:{todo.color}">
                <input type="checkbox" checked={todo.done} on:click={() => todo.done = !todo.done}> 
                <div class="w-100">{todo.text}</div> 
                <button  class="btn btn-sm btn-secondary" data-bs-toggle="collapse" on:click={() => loadEditTodo(index)} data-bs-target="#collapseTodo{index}" aria-expanded="false" aria-controls="collapseTodo">Edit</button> | <button type="button" class="btn btn-sm btn-danger" on:click={() => removeTodoItem(todo)}>Delete</button>
            </div>
            <div id="collapseTodo{index}" class="collapse todoEdit" data-bs-parent="#todoaccordion">
                <div class="input-group mb-3">
                    <span class="input-group-text">Text:</span>
                    <input id="todoupdate{index}" class="form-control" bind:value={todoitemupdate} />
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">Background:</span>
                    <input type="color" class="colorpicker" bind:value={todocolorupdate}>
                </div>
                <button class="btn btn-success" data-bs-toggle="collapse" data-bs-target="#collapseTodo{index}" on:click={() => editTodoItem(index)} >Update</button>
            </div>
        {/each}
        {:else}
            <div>Nothing ToDo!</div>
        {/if}
    </div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 390px) {
		main {
			max-width: none;
		}
	}
    .list-group-item {
        text-align: left;
    }

    .todolist {
        margin-top:10px;
    }
    .todoitem {
        display: flex;
        align-items: center;
        font-size:1.1em;
    }
    .todoitem div {
        padding-left:10px;
    }
    .todoitem button {
        float:left;
    }
    input[type="checkbox"] {
            width: 2.2em;
            height: 2.2em;
        }
    .todoEdit {
        padding:10px;
        border-left:1px solid #dddddd;
        border-right:1px solid #dddddd;
        border-bottom:1px solid #dddddd;
        background-color:#fafafa;
    }
    .colorpicker {
        height:2.4em;
    }
</style>