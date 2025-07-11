<script lang="ts">
       import { onMount } from 'svelte';
       import { browser } from '$app/environment';
       type Task = { text: string; completed: boolean };
       let task: string = '';
       let tasks: Task[] = [];
       let filter: 'all' | 'active' | 'completed' = 'all';
       let editingIndex: number | null = null;
       let editText: string = '';

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

       function deleteAll() {
         tasks = [];
       }

       function startEditing(index: number) {
         editingIndex = index;
         editText = tasks[index].text;
       }

       function saveEdit() {
         if (editingIndex !== null && editText.trim()) {
           tasks[editingIndex].text = editText;
           tasks = tasks; // Trigger reactivity
           editingIndex = null;
           editText = '';
         }
       }
     </script>

     <div class="max-w-md mx-auto p-6 bg-white shadow-md rounded-lg">
       <h1 class="text-3xl font-extrabold text-blue-700 mb-6 text-center">Todo App</h1>
       <div class="mb-4 flex gap-2">
         <form on:submit|preventDefault={addTask} class="flex-1 flex gap-2">
           <input
             type="text"
             bind:value={task}
             placeholder="Add a task"
             class="border rounded p-2 flex-1 focus:outline-none focus:ring-2 focus:ring-blue-500"
           />
           <button
             type="submit"
             class="bg-blue-600 text-white px-4 py-2 rounded-md font-semibold tracking-wide hover:bg-blue-700 transition-colors shadow-sm"
           >
             Add
           </button>
         </form>
         <button
           class="bg-red-800 text-white px-4 py-2 rounded-md font-semibold tracking-wide hover:bg-red-900 transition-colors shadow-sm"
           on:click={deleteAll}
         >
           Delete All
         </button>
       </div>
       <div class="mb-4 flex flex-wrap gap-2">
         <button
           class="{filter === 'all' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-700'} px-3 py-1 rounded-md font-semibold tracking-wide hover:bg-blue-500 hover:text-white transition-colors shadow-sm"
           on:click={() => (filter = 'all')}
         >
           All
         </button>
         <button
           class="{filter === 'active' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-700'} px-3 py-1 rounded-md font-semibold tracking-wide hover:bg-blue-500 hover:text-white transition-colors shadow-sm"
           on:click={() => (filter = 'active')}
         >
           Active
         </button>
         <button
           class="{filter === 'completed' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-700'} px-3 py-1 rounded-md font-semibold tracking-wide hover:bg-blue-500 hover:text-white transition-colors shadow-sm"
           on:click={() => (filter = 'completed')}
         >
           Completed
         </button>
         <button
           class="bg-red-600 text-white px-3 py-1 rounded-md font-semibold tracking-wide hover:bg-red-700 transition-colors shadow-sm"
           on:click={clearCompleted}
         >
           Clear Completed
         </button>
       </div>
       <ul class="space-y-2">
         {#each filteredTasks as task, index}
           <li class="flex justify-between items-center border p-2 rounded">
             <div class="flex items-center flex-1">
               <input
                 type="checkbox"
                 checked={task.completed}
                 on:change={() => toggleTask(index)}
                 class="mr-2"
               />
               {#if editingIndex === index}
                 <input
                   type="text"
                   bind:value={editText}
                   on:blur={saveEdit}
                   on:keydown={e => e.key === 'Enter' && saveEdit()}
                   class="border rounded p-2 flex-1 focus:outline-none focus:ring-2 focus:ring-blue-500"
                 />
               {:else}
                 <span class={task.completed ? 'line-through text-gray-500' : ''}>
                   {task.text}
                 </span>
               {/if}
             </div>
             <div class="flex gap-2">
               {#if editingIndex !== index}
                 <button
                   on:click={() => startEditing(index)}
                   class="text-blue-600 hover:text-blue-800 font-semibold transition-colors"
                 >
                   Edit
                 </button>
               {/if}
               <button
                 on:click={() => removeTask(index)}
                 class="text-red-600 hover:text-red-800 font-semibold transition-colors"
               >
                 Delete
               </button>
             </div>
           </li>
         {/each}
       </ul>
     </div>