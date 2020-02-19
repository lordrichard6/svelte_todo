<script>
    import TodoItem from './TodoItem.svelte';
    
    let todos = [
        {
            id: 1,
            title: 'Clean the house',
            completed: false
        },
        {
            id: 2,
            title: 'Study',
            completed: false
        },
        {
            id: 3,
            title: 'Go to Gim',
            completed: false
        },
    ];
    

    let newTodoTitle = '';
    let currentFilter = 'all';
    let nextId = 4;

    function addTodo(event) {
        if (event.key === 'Enter') {
            todos = [...todos, {
                id: nextId,
                completed: false,
                title: newTodoTitle
            }];        
            nextId = nextId + 1;
            newTodoTitle = '';
        }
    }

    $: todosRemaining = filteredTodos.filter(todo => !todo.completed).length;
    $: filteredTodos = currentFilter === 'all' ? todos : currentFilter === 'completed'
        ? todos.filter(todo => todo.completed)
        : todos.filter(todo => !todo.completed)

    function checkAllTodos(event) {
        todos.forEach(todo => todo.completed = event.target.checked);
        todos = todos;
    }

    // As a parameter this function is taking the newFilter value and the setting variable currentFilter to this new value. 
    // Because currentFilter is used in the filteredTodos computed property, 
    // the value of the computed property is updated automatically due to reactivity in Svelte. 
    function updateFilter(newFilter) {
        currentFilter = newFilter;
    }

    // The purpose of this function is to remove all todo items with status completed from the todos array
    function clearCompleted() {
        todos = todos.filter(todo => !todo.completed);
    }

    function handleDeleteTodo (event) {
        todos = todos.filter(todo => todo.id !== event.detail.id);
    }

    function handleToggleComplete(event) {
    const todoIndex = todos.findIndex(todo => todo.id === event.detail.id);
    const updatedTodo = { ...todos[todoIndex], completed: !todos[todoIndex].completed};
    todos = [
        ...todos.slice(0, todoIndex),
        updatedTodo,
        ...todos.slice(todoIndex + 1),
    ];
}
</script>
<style>
    .container {
        max-width: 800px;
        margin: 10px auto;
    }
    .logo {
        display: block;
        margin: 20px auto;
        width: 50%;
    }
    .todo-input {
        width: 100%;
        padding: 10px, 20px;
        font-size: 18px;
        margin-bottom: 20px;
    }
    .inner-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgrey;
        padding-top: 15px;
        margin-bottom: 13px;
    }
    .inner-container-input {
        margin-right: 12px;
    }
    button {
        font-size: 14px;
        background-color: white;
        appearance: none;
    }
    button:hover {
        background: lightsteelblue;
    }
    button:focus {
        outline:none;
    }
    .active {
        background: lightsteelblue;
    }
</style>
<div class="container">
    <!-- <a href="https://www.pauloreizinho.com/" target="_blank"> -->
    <img src={'/img/logo.png'} alt="svelte logo" class="logo">
    <!-- </a> -->
    <h2>What do you have to DO today?</h2>
    <input type="text" class="todo-input" placeholder="Insert todo item ..." bind:value={newTodoTitle} on:keydown={addTodo}>

    {#each filteredTodos as todo}
        <div class="todo-item">
            <TodoItem {...todo} on:deleteTodo={handleDeleteTodo} on:toggleComplete={handleToggleComplete} />
        </div>
    {/each}
    
    <div class="inner-container">
        <div><label><input class="inner-container-input" type="checkbox" on:change={checkAllTodos}>Check All</label></div>
        <div>{todosRemaining} items left</div>
    </div>

    <div class="inner-container">
        <div>
            <button on:click={() => updateFilter('all')} class:active="{currentFilter === 'all'}">All</button>
            <button on:click={() => updateFilter('active')} class:active="{currentFilter === 'active'}">Active</button>
            <button on:click={() => updateFilter('completed')} class:active="{currentFilter === 'completed'}">Completed</button>
        </div>

        <div>
            <button on:click={clearCompleted}>Clear Completed</button>
        </div>
    </div>
</div>