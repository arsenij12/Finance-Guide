<template>
  <div class="form-container">
    <!-- Форма добавления новой транзакции -->
    <div>
      <input v-model="newTransaction.amount" type="number" placeholder="Сумма">
      <input v-model="newTransaction.date" type="date" placeholder="Дата">
      <select v-model="newTransaction.category">
        <option value="Питание">Питание</option>
        <option value="Транспорт">Транспорт</option>
        <option value="Развлечения">Развлечения</option>
        <!-- Добавьте другие категории по вашему выбору -->
      </select>
      <button @click="addTransaction">Добавить транзакцию</button>
    </div>
    
    <!-- Список добавленных транзакций -->
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
        category: 'Питание' // Значение по умолчанию для категории
      },
      transactions: [] // Массив для хранения транзакций
    };
  },
  methods: {
    // Добавление новой транзакции
    addTransaction() {
      if (this.newTransaction.amount !== null && this.newTransaction.date !== null) {
        this.transactions.push({...this.newTransaction});
        this.resetForm();
      } else {
        alert('Пожалуйста, заполните все поля');
      }
    },
    // Сброс формы после добавления транзакции
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

<style>
/* Стили для формы */
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

/* Стили для списка транзакций */
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
