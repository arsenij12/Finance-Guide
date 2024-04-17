<template>
  <div>
    <input v-model="newItemText" @keyup.enter="addItem" placeholder="Введите новый элемент">
    
    <ul>
      <li v-for="(item, index) in items" :key="index">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newItemText: '',
      items: []
    };
  },
  methods: {
    addItem() {
      if (this.newItemText.trim() !== '') {
        this.items.push(this.newItemText);
        this.newItemText = ''; 
      }
    }
  }
};
</script>

<style>
ul {
  list-style-type: none;
  padding: 0;
}
</style>
