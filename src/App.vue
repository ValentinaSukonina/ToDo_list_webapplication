<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import Header from './components/Header.vue';
import ListItem from './components/ListItem.vue'
import ListForm from './components/ListForm.vue';
import { nanoid } from 'nanoid'

const ListItems = ref([])

const fetchListItems = async () => {
  try {
    const response = await fetch('/tasks.json'); 
    if (!response.ok) throw new Error('Failed to fetch tasks');
    const data = await response.json();
    // Optionally assign new IDs
    return data.map(task => ({
      ...task,
      id: nanoid() 
    }));
  } catch (error) {
    console.error('Error fetching tasks:', error);
    return [];
  }
};

//Function to save ListItems to local storage
const saveListItems = (items) => {
  localStorage.setItem('listItems',JSON.stringify(items))
} 

const loadListItems = async () => {      
  try {
    // Check if the list has been initialized in local storage
    const isInitialized = localStorage.getItem('isInitialized');
    if (!isInitialized) {
      // If not initialized, fetch from JSON 
      const initialItems = await fetchListItems();
      localStorage.setItem('listItems', JSON.stringify(initialItems));
      localStorage.setItem('isInitialized', 'true');  
      return initialItems;
    } else {
      // If initialized, load from local storage
      const items = localStorage.getItem('listItems');
      return items ? JSON.parse(items) : [];
    }
  } catch (error) {
    console.error('Error loading tasks:', error);
    return [];
  } 
}

// Watch for changes in ListItems and save them to local storage
watch(
  ListItems,
  (newItems) => { saveListItems(newItems); },
  { deep: true }
);

onMounted(async () => {
  ListItems.value = await loadListItems();
});

const updateDoneStatus = (itemId) => {
  const task = ListItems.value.find(item => item.id === itemId);
  if (task) {
    task.done = !task.done;
    console.log("task done");
  } else {
    console.error("Task not found with ID:", itemId);
  }
}

const updatePriorityStatus = (itemId, newPriority) => {
  console.log(`Received priority change for ID ${itemId} to ${newPriority}`);
  const task = ListItems.value.find(item => item.id === itemId);
  if (task) {
    task.priority = newPriority;
    console.log("Priority updated for item ID:", itemId, "to", newPriority);
  } else {
    console.error("Task not found with ID:", itemId);
  }
}

const listSummary = computed(() => {
  const completedTasks = ListItems.value.filter(item => item.done).length;
  const totalTasks = ListItems.value.length;
  return `Tasks completed: ${completedTasks} out of ${totalTasks}`;
});

const addItem = (itemLabel) => {
  console.log("New item added: ", itemLabel);
  if (!itemLabel) {
    console.error("Submission of empty field");
    return;
  }
  ListItems.value.push({
    id: "list-" + nanoid(),
    label: itemLabel,
    done: false
  });
};

const deleteItem = (itemId) => {
  const index = ListItems.value.findIndex(item => item.id === itemId);
  if (index !== -1) {
    ListItems.value.splice(index, 1);
    console.log("Item deleted", itemId);
  } else {
    console.error("Item not found with ID:", itemId);
  }
}

const editItem = (itemId, updatedLabel) => {
  const index = ListItems.value.findIndex(item => item.id === itemId);
  if (index !== -1) {
    ListItems.value[index].label = updatedLabel;  // Update the label correctly
    console.log("Updated Item:", ListItems.value[index]);
  } else {
    console.error("Item not found with ID:", itemId);
  }
};
</script>

<template>
  <div id="app">
    <Header />
    <div class="container">
      <h2>Plan Your Day, Task by Task</h2>
      <div class="formPlace" >
        <list-form @item-added="addItem"></list-form>
      </div>
      <h2 id="count-summary" tabindex="-1">{{ listSummary }}</h2>
      <ul aria-labelledby="list-summary" class="stack-large">
        <li v-for="item in ListItems" :key="item.id">
          <list-item
            :id="item.id"
            :label="item.label"
            :done="item.done"
            :priority="item.priority"
            @checkbox-changed="updateDoneStatus"
            @priority-changed="updatePriorityStatus"
            @item-deleted="deleteItem"
            @item-edited="editItem"
          ></list-item>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
h1 {
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-weight: bold;
  font-size: calc(3vw + 10px);  
}
.stack-large {
  padding: 10px 20px;
}

.formPlace {
  display: grid;
  grid-column: 1 / -1;
  margin: 10px 10px;
}

#count-summary {
  display: flex;
  justify-content: center;  
  font-size: clamp(18px, calc(1.5vw + 10px), 22px);  
  
}

.btn {
  flex: 1; 
  padding: 0.4rem 0.2rem 0.2rem;
  border: 0.15rem solid #4a5d3c;
  border-radius: 5px;
  background-color: #b7d2ab;
  cursor: pointer;
  font-weight: bold;  
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: calc(0.5vw + 10px);
  margin: 0 5px;
}

  .visually-hidden {
    position: absolute;
    height: 1px;
    width: 1px;
    overflow: hidden;
    clip: rect(1px 1px 1px 1px);
    clip: rect(1px, 1px, 1px, 1px);
    clip-path: rect(1px, 1px, 1px, 1px);
    white-space: nowrap;
  }

  .btn:hover {
  background-color: #bdcd91;
}

@media (max-width: 700px) {
  .stack-large {
  padding: 10px 10px;
}
  .container  {
    padding: 5px 5px;;
  }
  #count-summary{
    padding: 10px 10px; 
  }
}

@media (max-width: 400px) {
  .container h2 {
    display: none;
  }

  .formPlace {
  display: grid;
  grid-column: 1 / -1;  
  margin: 5px 5px;
} 

  #count-summary {
  display: flex;
  justify-content: center; 
  padding: 5px 10px;
}

.stack-large {
  padding: 5px 5px;
}
  .btn {
    flex: 1; 
    padding: 0.2rem 0.2rem 0.2rem;  
    font-size: 14px;
    margin: 0 5px;
  }
}
</style>

