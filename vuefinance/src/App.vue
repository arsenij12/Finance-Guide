<template>
  <div>
    <header class="header">
      <div class="header-container">
        <v-dialog v-model="dialog" max-width="400" persistent>
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn v-bind="activatorProps" class="dialog-btn">
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 0 24 24" width="24px" fill="#000000"><path d="M0 0h24v24H0V0z" fill="none"/><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm5 11h-4v4h-2v-4H7v-2h4V7h2v4h4v2z"/></svg>
              Open Dialog
            </v-btn>
          </template>

          <v-card class="dialog-card">
            <v-card-title class="dialog-title">
              Введите прибыль за месяц
            </v-card-title>
            <v-card-text class="dialog-content">
              <form>
                <input type="number" placeholder="Месячная прибыль">
              </form>
            </v-card-text>
            <template v-slot:actions>
              <div class="dialog-actions">
                <v-btn @click="dialog = false" class="dialog-btn">
                  Disagree
                </v-btn>
                <v-btn @click="dialog = false" class="dialog-btn">
                  Agree
                </v-btn>
              </div>
            </template>
          </v-card>
        </v-dialog>
      </div>
    </header>
    <main>
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
    </main>
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
      transactions: [],
      dialog: false
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
.dialog-btn {
  color: #1976d2;
}

.dialog-content {
  padding: 20px;
}

.dialog-actions {
  display: flex;
  justify-content: flex-end;
  padding: 8px;
}

.dialog-card {
  max-width: 400px;
}

.dialog-title {
  font-size: 20px;
  font-weight: bold;
}
.header{
  background-color: #0beb2962;
  color: #fff;
  padding: 20px;
  text-align: center;
}
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

.icon{
  color: black;
}
</style>
