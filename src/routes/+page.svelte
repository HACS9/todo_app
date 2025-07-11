<script lang="ts">
       import { onMount } from 'svelte';
       import { browser } from '$app/environment';
       type Task = { text: string; completed: boolean };
       let task: string = '';
       let tasks: Task[] = [];
       let filter: 'all' | 'active' | 'completed' = 'all';

       // Load tasks from localStorage
       onMount(() => {
         if (browser) {
           const saved = localStorage.getItem('tasks');
           if (saved) tasks = JSON.parse(saved);
         }
       });

       // Save tasks to localStorage
       $: {
         if (browser) {
           localStorage.setItem('tasks', JSON.stringify(tasks));
         }
       }

       // Filter tasks
       $: filteredTasks = tasks.filter(task => {
         if (filter === 'active') return !task.completed;
         if (filter === 'completed') return task.completed;
         return true;
       });

       function addTask() {
         if (task.trim()) {
           tasks = [...tasks, { text: task, completed: false }];
           task = '';
         }
       }

       function toggleTask(index: number) {
         tasks[index].completed = !tasks[index].completed;
         tasks = tasks; // Trigger reactivity
       }

       function removeTask(index: number) {
         tasks = tasks.filter((_, i) => i !== index);
       }

       function clearCompleted() {
         tasks = tasks.filter(task => !task.completed);
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
       <div class="mb-4">
         <button
           class="mr-2 px-2 py-1 {filter === 'all' ? 'bg-blue-500 text-white' : 'bg-gray-200'}"
           on:click={() => (filter = 'all')}
         >
           All
         </button>
         <button
           class="mr-2 px-2 py-1 {filter === 'active' ? 'bg-blue-500 text-white' : 'bg-gray-200'}"
           on:click={() => (filter = 'active')}
         >
           Active
         </button>
         <button
           class="px-2 py-1 {filter === 'completed' ? 'bg-blue-500 text-white' : 'bg-gray-200'}"
           on:click={() => (filter = 'completed')}
         >
           Completed
         </button>
       </div>
       <button
         class="mb-4 bg-red-500 text-white p-2 rounded hover:bg-red-600"
         on:click={clearCompleted}
       >
         Clear Completed
       </button>
       <ul class="space-y-2">
         {#each filteredTasks as task, index}
           <li class="flex justify-between items-center border p-2 rounded">
             <div class="flex items-center">
               <input
                 type="checkbox"
                 checked={task.completed}
                 on:change={() => toggleTask(index)}
                 class="mr-2"
               />
               <span class={task.completed ? 'line-through text-gray-500' : ''}>
                 {task.text}
               </span>
             </div>
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