<script setup>
import { ref, defineProps, defineEmits, onMounted } from 'vue';

const props = defineProps({
  label: { type: String, required: true },
  id: { type: String, required: true }
});

const emit = defineEmits(['item-edited', 'edit-cancelled']);
const newLabel = ref(props.label);

const labelInputRef = ref(null);
onMounted(() => {
  if (labelInputRef.value) {
    labelInputRef.value.focus();
  }
});      

// Function to handle form submission
const onSubmit = () => {
  console.log('Attempting to submit:', newLabel.value);
  // Check if the new label is different from the original
  if (newLabel.value !== props.label) {
    console.log('Submitting new label:', newLabel.value);
    emit('item-edited', props.id, newLabel.value);  // Emit the ID and the new label
  } else {
    console.log('No changes were made.');
    emit('edit-cancelled');
  }
};

const onCancel = () => {
  console.log('Cancelling edit');
  emit('edit-cancelled');
};
</script>

<template>
  <form class="editForm" @submit.prevent="onSubmit">
    <div class="stack-edit">
      <label class="edit-label" :for="props.id">Edit: </label>
      <input class="textToEdit"
        :id="props.id"
        ref="labelInputRef"
        type="text"
        autocomplete="off"
        v-model.lazy.trim="newLabel"
      />
    </div>
    <div class="btn-group">
      <button type="button" class="btn" @click="onCancel">
        Cancel
        <span class="visually-hidden">editing {{ label }}</span>
      </button>
      <button type="submit" class="btn btn__primary">
        Save
        <span class="visually-hidden">edit for {{ label }}</span>
      </button>
    </div>
  </form>
</template>

<style scoped>
.editForm {
  display: flex;
  flex-direction: row;
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: 16px;
  padding-left: 10px;
  gap: 10px; 
}

.textToEdit {
  padding: 3px 5px;
  font-size: clamp(16px, calc(0.5rem + 1vw), 18px);
  border-radius: 5px;
  padding-left: 5px;
  margin-left: 5px;
  width: 300px;
}

.textToEdit:focus {
  outline: none; 
  border: 3px solid #4a5d3c; 
}

.btn-group {
  padding-left: 5px;
  display: flex;
  flex-direction: row;
  gap: 5px; 
}

@media (max-width: 700px) {
  .editForm {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2px;
    padding-left: 0;
  }

  .stack-edit {
    grid-column: span 2;
    display: flex;
    flex-direction: row;
    padding-left: 0;
  }

  .btn-group {
    grid-column: span 2;
    display: flex;
    flex-direction: row;
    padding: 5px 10px;
    margin-left: 25px;
  }

  .textToEdit {
    margin-left: 5px;
  }
}

@media (max-width: 400px) {
  .container h2 {
    display: none;
  }
}
</style>


