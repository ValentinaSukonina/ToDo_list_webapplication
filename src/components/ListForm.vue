<script setup>
import { ref, defineEmits } from 'vue';

const label = ref("");  // This holds the input value
const emits = defineEmits(['item-added']);  // Define an event that this component can emit

const onSubmit = () => {
    console.log("form submitted");
    console.log("Label: ", label.value);
    emits('item-added', label.value);  // Emit the value to the parent
    label.value = "";  // Reset the label after submission
};

</script>

<template>     
  <form class="addItem" @submit.prevent="onSubmit">
    <div class="form-grid"> 
      <label class="item1" for="new-input">Add new task to the list</label>
      <input class="item2" placeholder="Enter new task" required
        type="text" 
        id="new-input" 
        name="new-item"
        autocomplete="off" 
        v-model.trim.lazy="label"/>
      <button class="btn" id="btnSubmit" type="submit">Add</button>
    </div>
  </form>
  
</template>

<style scoped>
.form-grid {
  display: grid;
  grid-template-columns:  1.3fr 1fr 0.7fr;
  gap: 5px;
  margin: 5px 5px;
}

.item1 {
  grid-column: 1 / -1;
  display: flex;
  justify-content: left;  
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: calc(1.5vw + 10px);
  font-weight: bold;
  color: #4a5d3c;
  margin-left: 10px;   
}  

.item2 {
  grid-column: 1 / 3;   
  margin-left: 10px;   
  margin-right: 10px;
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: 16px;
  padding: 7px 10px;
  cursor: pointer;
    font-weight: bold;
    border-radius: 4px;
}

.item2:focus {
  outline: none; /* Removes the default focus outline */
  border: 3px solid #4a5d3c; /* Adds a green border when focused */
}

#btnSubmit {  
  grid-column: 3; 
  margin-right: 5px;   
}

@media (max-width: 400px) {
  .form-grid {
    grid-template-columns:  1.5fr 1fr 0.5fr;
    display: grid;
    gap: 2px;
    margin: 5px 2px;
  }

  .item1 {  
    grid-column: 1 / -1;
    display: flex;
    font-size: 18px;
    font-weight: bold;  
    margin-left: 0px;       
}  

.item2 {  
  grid-column: 1 / 3;   
  margin-left: 0px;   
  margin-right: 5px;
  font-family: Georgia, 'Times New Roman', Times, serif;
  font-size: 16px;
  padding: 5px 10px;
  cursor: pointer;
  font-weight: bold;
  border-radius: 4px;  
}

.item2:focus {
  outline: none; /* Removes the default focus outline */
  border: 3px solid #4a5d3c; /* Adds a green border when focused */
}

#btnSubmit {  
  grid-column: 3; 
  margin-right: 0px;   
}
}
</style>
