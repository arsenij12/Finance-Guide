<template>
  <div class="form-container">
    <div>
      <input v-model="newTransaction.amount" type="number" placeholder="Сумма">
      <input v-model="newTransaction.date" type="date" placeholder="Дата">
      <select v-model="newTransaction.category">
        <option value="Питание">Питание</option>
        <option value="Транспорт">Транспорт</option>
        <option value="Развлечения">Развлечения</option>
      </select>
      <button @click="addTransaction">Добавить транзакцию</button>
    </div>
      <ul class="transaction-list">
      <li v-for="(transaction, index) in transactions" :key="index">
        {{ transaction.amount }} - {{ transaction.date }} - {{ transaction.category }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTransaction: {
        amount: null,
        date: null,
        category: 'Питание' 
      },
      transactions: [] 
    };
  },
  mounted() {
    const savedTransactions = localStorage.getItem('transactions');
    if (savedTransactions) {
      this.transactions = JSON.parse(savedTransactions);
    }
  },
  methods: {
    addTransaction() {
      if (this.newTransaction.amount !== null && this.newTransaction.date !== null) {
        this.transactions.push({...this.newTransaction});
        localStorage.setItem('transactions', JSON.stringify(this.transactions));
        this.resetForm();
      } else {
        alert('Пожалуйста, заполните все поля');
      }
    },
    resetForm() {
      this.newTransaction = {
        amount: null,
        date: null,
        category: 'Питание'
      };
    }
  }
};
</script>

<style scoped>
.form-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.form-container input[type="number"],
.form-container input[type="date"],
.form-container select {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

.form-container button {
  width: 100%;
  padding: 10px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.form-container button:hover {
  background-color: #45a049;
}

.transaction-list {
  list-style-type: none;
  padding: 0;
}

.transaction-list li {
  background-color: #f0f0f0;
  padding: 10px;
  margin-bottom: 5px;
  border-radius: 5px;
}
</style>
