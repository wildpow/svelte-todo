<script>
  import TodoItem from "./TodoItem.svelte";

  let newTodoTitle = "";
  let currentFilter = "all";
  let nextId = 4;

  let todos = [
    {
      id: 1,
      title: "My first todo",
      completed: false
    },
    {
      id: 2,
      title: "My second todo",
      completed: false
    },
    {
      id: 3,
      title: "My third todo",
      completed: false
    }
  ];

  function addTodo(event) {
    if (event.key === "Enter") {
      todos = [...todos, { id: nextId, completed: false, title: newTodoTitle }];
      nextId = nextId + 1;
      newTodoTitle = "";
    }
  }

  $: todosRemaining = filteredTodos.filter(todo => !todo.completed).length;
  $: filteredTodos =
    currentFilter === "all"
      ? todos
      : currentFilter === "completed"
      ? todos.filter(todo => todo.completed)
      : todos.filter(todo => !todo.completed);

  function checkAllTodos(event) {
    todos.forEach(todo => (todo.completed = event.target.checked));
    todos = todos;
  }

  function updateFilter(newFilter) {
    currentFilter = newFilter;
  }

  function clearCompleted(event) {
    todos = todos.filter(todo => !todo.completed);
  }

  function handleDeleteTodo(event) {
    todos = todos.filter(todo => todo.id !== event.detail.id);
  }

  function handleToggleComplete(event) {
    const todoIndex = todos.findIndex(todo => todo.id === event.detail.id);
    const updatedTodo = {
      ...todos[todoIndex],
      completed: !todos[todoIndex].completed
    };

    todos = [
      ...todos.slice(0, todoIndex),
      updatedTodo,
      ...todos.slice(todoIndex + 1)
    ];
  }
</script>

<style>
  .btn {
    @apply font-bold py-2 px-4 rounded;
  }
  .btn-blue {
    @apply bg-blue-500 text-white;
  }
  .btn-blue:hover {
    @apply bg-blue-700;
  }
</style>

<div class="w-full max-w-2xl mx-auto">
  <h1 class="text-gray-500 text-3xl">Svelte Todos</h1>
  <div class="bg-white shadow-md rounded px-8 pt-8 pb-8 mb-4 mt-2">

    <input
      type="text"
      class="shadow appearance-none border rounded w-full py-2 px-3
      text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
      placeholder="Insert todo item..."
      bind:value={newTodoTitle}
      on:keydown={addTodo} />
    <div class="py-4 bg-white">

      {#each filteredTodos as todo}
        <TodoItem
          {...todo}
          on:deleteTodo={handleDeleteTodo}
          on:toggleComplete={handleToggleComplete} />
      {/each}
    </div>

    <div
      class="flex justify-between mb-4 px-2 text-gray-500 font-bold items-center">
      <label class="flex items-center">
        <input
          type="checkbox"
          class="flex leading-tight"
          on:change={checkAllTodos} />
        <span class="pl-2">Check All</span>
      </label>
      <div>{todosRemaining} items left</div>
    </div>

    <div class="flex justify-between ">
      <div>
        <button
          class="btn btn-blue"
          on:click={() => updateFilter('all')}
          class:active={currentFilter === 'all'}>
          All
        </button>
        <button
          class="btn btn-blue"
          on:click={() => updateFilter('active')}
          class:active={currentFilter === 'active'}>
          Active
        </button>
        <button
          class="btn btn-blue"
          on:click={() => updateFilter('completed')}
          class:active={currentFilter === 'completed'}>
          completed
        </button>
      </div>
      <div>
        <button class="btn btn-blue" on:click={clearCompleted}>
          Clear Completed
        </button>
      </div>
    </div>
  </div>
</div>
