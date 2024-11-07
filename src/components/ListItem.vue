<script setup>
import ItemEditForm from './ItemEditForm.vue';
import { ref, defineProps, defineEmits, computed } from 'vue';

const props = defineProps({
  label: { type: String, required: true },
  done: { type: Boolean, default: false },
  id: { type: String, required: true },
  priority: { type: Boolean, default: false }
});


const emit = defineEmits(['item-deleted', 'item-edited', 'checkbox-changed', 'priority-changed']);

const priorityStatus = computed({
  get: () => props.priority,
  set: () => {
    let toggledPriority = !props.priority; 
    console.log(`Emitting priority change for ID ${props.id} to ${toggledPriority}`);
    emit('priority-changed', props.id, toggledPriority);
  }
});

const addPriority = () => {
  console.log('Emitting priority change for ID:', props.id);
  priorityStatus.value = !props.priority;
  buttonRef.value?.blur();
};

const buttonRef = ref(null);

const deleteItem = () => {
  console.log("Deleting task:", props.id);
  emit("item-deleted", props.id);
};

// Toggle edit mode
const isEditing = ref(false);

const toggleItemEditForm = () => {
  isEditing.value = true;
};


const itemEdited = (itemId, newLabel) => {
  emit('item-edited', itemId, newLabel);  
  isEditing.value = false;
  console.log("New label submitted:", newLabel);
};

const editCancelled = () => {
  isEditing.value = false;
};

const isDone = computed({
  get: () => props.done,
  set: (newVal) => emit('checkbox-changed', props.id, newVal)
});
</script>

<template>
  <div class="stack-small" v-if="!isEditing">
    <div class="custom-checkbox">
      <input type="checkbox" :id="`checkbox-${id}`" v-model="isDone"/>
      <label :class="{ 'high-priority': priority }" 
      :for="`checkbox-${id}`">{{ label }}</label>
    </div>
    <div class="btnGroup">
      <button class="btn" id="priorityBtn" type="button" 
      ref="buttonRef" @click="addPriority">
        Priority <span class="visually-hidden">{{ label }}</span>
      </button>
      <button class="btn" type="button" @click="toggleItemEditForm">
        Edit <span class="visually-hidden">{{ label }}</span>
      </button>
      <button class="btn" type="button" @click="deleteItem">
        Delete <span class="visually-hidden">{{ label }}</span>
      </button>            
    </div>
  </div>
  <item-edit-form v-else 
  :id="id" 
  :label="label"
  @item-edited="itemEdited"
  @edit-cancelled="editCancelled"
  ></item-edit-form>
</template>

<style scoped>
.stack-small {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 10px;  
  margin: 10px 10px;;
}

.custom-checkbox {
  grid-column: 1 / -1;
  display: flex;
  justify-content: left;  
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: clamp(16px, calc(1.2vw + 10px), 18px);
  
  padding-right: 20px;
}

.btnGroup{
  grid-column: 1 / -1;   
  display: flex;
  margin-left: 40px;   
  gap: 5px;
  padding-bottom: 10px;
}
.high-priority {
  color: #CD5C08;
  font-style: bold;
}

/* Scoped styles for ListItem component */
.custom-checkbox {
  display: flex;
  align-items: center;
  position: relative;
}

.custom-checkbox input[type="checkbox"] {
  appearance: none;
  background-color: #fff;
  margin: 0;
  font: inherit;
  color: currentColor;
  width: 1.2em;
  height: 1.2em;
  border: 2px solid #757575;
  border-radius: 0.15em;
  transform: translateY(-0.075em);
  display: grid;
  place-content: center;
}

.custom-checkbox input[type="checkbox"]::before {
  content: "";
  width: 0.65em;
  height: 0.65em;
  clip-path: polygon(14% 44%, 50% 80%, 86% 20%, 70% 20%, 50% 60%, 30% 34%);
  transform: scale(0);
  transform-origin: bottom left;
  transition: 120ms transform ease-in-out;
  box-shadow: inset 1em 1em var(--check-color);
  /* Custom property for check color */
  --check-color: #fff;
}

.custom-checkbox input[type="checkbox"]:checked::before {
  transform: scale(1);
}

.custom-checkbox input[type="checkbox"]:checked {
  background: #4caf50;
  border-color: #4caf50;
}

.custom-checkbox input[type="checkbox"]:focus {
  outline: none;
  box-shadow: 0 0 0 2px rgba(76, 175, 80, 0.24);
}

.custom-checkbox label {
  font-size: calc(1.2vw + 10px);
  margin-left: 8px;
  cursor: pointer;
}

@media (max-width: 700px) {
  .stack-large {
  padding: 10px 10px;
}

.stack-small {
  gap: 5px;
  margin: 5px 5px;
}

.btnGroup{
  grid-column: 1 / -1;   
  display: flex;
  margin-left: 20px;   
  gap: 5px;
  padding-bottom: 10px;
}
.btn {
  padding: 5px auto;
  }
}

@media (max-width: 400px) {
  .btn {
  padding: 3px auto;
  }


}
</style>