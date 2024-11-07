<script setup>
</script>

<template>
    <header>      
        <h1>Today's Agenda at a Glance</h1>
    </header>
</template>

Header.vue
<style scoped>
header {  
  display: flex;  
  width: 100%; 
  max-width: 800px; 
  color:  #E7F0DC;    
  justify-content: center;
  text-align: center;
  background-color: #729762;
  padding: 15px;
  margin-top: 10px;
}

@media (max-width: 700px) {
  header {   
  padding: 10px 5px;
  margin-top: 5px;
  }
}

@media (max-width: 400px) {
  header {   
  padding: 10px 5px;
  margin-top: 5px;
  }
}
</style>