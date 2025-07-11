<script lang="ts">
  let task: string = '';
  let tasks: string[] = [];

  function addTask() {
    if (task.trim()) {
      tasks = [...tasks, task];
      task = '';
    }
  }

  function removeTask(index: number) {
    tasks = tasks.filter((_, i) => i !== index);
  }
</script>

<div class="max-w-md mx-auto p-4">
  <h1 class="text-2xl font-bold text-blue-600 mb-4">Todo App</h1>
  <form on:submit|preventDefault={addTask} class="mb-4">
    <input
      type="text"
      bind:value={task}
      placeholder="Add a task"
      class="border rounded p-2 w-full"
    />
    <button
      type="submit"
      class="mt-2 bg-blue-500 text-white p-2 rounded hover:bg-blue-600"
    >
      Add Task
    </button>
  </form>
  <ul class="space-y-2">
    {#each tasks as task, index}
      <li class="flex justify-between items-center border p-2 rounded">
        <span>{task}</span>
        <button
          on:click={() => removeTask(index)}
          class="text-red-500 hover:text-red-700"
        >
          Delete
        </button>
      </li>
    {/each}
  </ul>
</div>