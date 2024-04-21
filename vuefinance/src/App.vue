<template>
  <div>
    <header class="header">
      <div class="header-container">
        <v-dialog v-model="dialog" max-width="400" persistent>
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn v-if="activatorProps" v-bind="activatorProps" class="dialog-btn">
              <svg xmlns="http://www.w3.org/2000/svg" enable-background="new 0 0 24 24" height="24px" viewBox="0 0 24 24" width="24px" fill="#000000"><rect fill="none" height="24" width="24"/><path d="M12,2C6.48,2,2,6.48,2,12s4.48,10,10,10s10-4.48,10-10S17.52,2,12,2z M12,20c-4.41,0-8-3.59-8-8s3.59-8,8-8s8,3.59,8,8 S16.41,20,12,20z M16.17,14.76l-1.1-1.1c0.71-1.33,0.53-3.01-0.59-4.13C13.79,8.84,12.9,8.5,12,8.5c-0.03,0-0.06,0.01-0.09,0.01 L13,9.6l-1.06,1.06L9.11,7.83L11.94,5L13,6.06l-0.96,0.96c1.27,0.01,2.53,0.48,3.5,1.44C17.24,10.17,17.45,12.82,16.17,14.76z M14.89,16.17L12.06,19L11,17.94l0.95-0.95c-1.26-0.01-2.52-0.5-3.48-1.46c-1.71-1.71-1.92-4.35-0.64-6.29l1.1,1.1 c-0.71,1.33-0.53,3.01,0.59,4.13c0.7,0.7,1.63,1.04,2.56,1.01L11,14.4l1.06-1.06L14.89,16.17z"/></svg>
            </v-btn>
          </template>

          <v-card class="dialog-card">
            <v-card-title class="dialog-title">
              Введите прибыль за месяц
            </v-card-title>
            <v-card-text class="dialog-content">
              <form>
                <v-text-field v-model="monthlyProfitInput" label="Месячная прибыль" type="number"></v-text-field>
              </form>
            </v-card-text>
            <template v-slot:actions>
              <div class="dialog-actions">
                <v-btn @click="dialog = false" class="dialog-btn" color="red darken-1"> 
                  Disagree
                </v-btn>
                <v-btn  @click="agreeAndSave" class="dialog-btn" color="green darken-1"> 
                  Agree
                </v-btn>
              </div>
            </template>
          </v-card>
        </v-dialog>
        <h1 class="headline">
          {{ formattedRemainingProfit }}
        </h1>
        <p v-if="notification">{{ notification }}</p>
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
            <div class="transaction">
              <span>{{ formattedTransactionAmount(transaction.amount) }} - {{ transaction.date }} - {{ transaction.category }}</span>
              <button class="remove-btn" @click="removeTransaction(index)">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 0 24 24" width="24px" fill="#FF0000">
                  <path d="M0 0h24v24H0V0z" fill="none"/>
                  <path d="M16 9v10H8V9h8m-1.5-6h-5l-1 1H5v2h14V4h-3.5l-1-1zM18 7H6v12c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7z"/>
                </svg>
              </button>
            </div>
          </li>
        </ul>
        <BarChart :chartdata="chartData" :options="chartOptions"/>
      </div>
    </main>
  </div>
</template>

<script>
import BarChart from './components/BarChart';

export default {
  name: 'BarChar',
  components: { BarChart },
  data() {
    return {
      chartData: {
        labels: [
          'January',
          'February',
          'March',
          'April',
          'May',
          'June',
          'July',
          'August',
          'September',
          'October',
          'November',
          'December'
        ],
        datasets: [
          {
            label: 'Data One',
            data: [40, 20, 12, 39, 10, 40, 39, 80, 40, 20, 12, 11]
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false
      },
      newTransaction: {
        amount: null,
        date: null,
        category: 'Питание' 
      },
      incomeData: [], 
      expensesData: [],
      transactions: [],
      dialog: false,
      monthlyProfitInput: null,
      monthlyProfit: null,
      monthlySpent: null,
      notification: '',
    };
  },
  mounted() {
    this.loadTransactions(); 
    this.loadMonthlyData();
  },
  computed: {
    remainingProfit() {
      return this.monthlyProfit - this.monthlySpent;
    },
    formattedRemainingProfit() {
      return new Intl.NumberFormat('ru-RU', { style: 'currency', currency: 'RUB' }).format(this.remainingProfit);
    },
  },
  methods: {
    removeTransaction(index) {
      const removedTransaction = this.transactions[index];
      this.transactions.splice(index, 1);
      this.updateMonthlySpent();
      this.updateChartData(removedTransaction);
      this.saveTransactions();
    },
    addTransaction() {
      if (this.newTransaction.amount !== null && this.newTransaction.date !== null) {
        this.transactions.push({ ...this.newTransaction });
        this.updateMonthlySpent();
        this.updateChartData(this.newTransaction);
        this.saveTransactions();
        this.resetForm();
      } else {
        alert('Пожалуйста, заполните все поля');
      }
    },
    updateChartData(transaction) {
      if (transaction.amount >= 0) {
        this.incomeData.push({
          month: transaction.date, 
          amount: parseFloat(transaction.amount)
        });
      } else {
        this.expensesData.push({
          month: transaction.date,
          amount: parseFloat(transaction.amount)
        });
      }
    },
    agreeAndSave() {
      if (this.monthlyProfitInput !== null) {
        localStorage.setItem('monthlyProfit', this.monthlyProfitInput);
        this.dialog = false;
        this.monthlyProfit = parseFloat(this.monthlyProfitInput);
      } else {
        alert('Введите значение месячной прибыли');
      }
    },
    resetForm() {
      this.newTransaction = {
        amount: null,
        date: null,
        category: 'Питание'
      };
    },
    formattedTransactionAmount(amount) {
      return new Intl.NumberFormat('ru-RU', { style: 'currency', currency: 'RUB' }).format(amount);
    },
    updateMonthlySpent() {
      this.monthlySpent = this.transactions.reduce((total, transaction) => total + parseFloat(transaction.amount), 0);
      localStorage.setItem('monthlySpent', this.monthlySpent);
    },
    saveTransactions() {
      localStorage.setItem('transactions', JSON.stringify(this.transactions));
    },
    loadTransactions() {
      const savedTransactions = localStorage.getItem('transactions');
      if (savedTransactions) {
        this.transactions = JSON.parse(savedTransactions);
      }
    },
    loadMonthlyData() {
      const savedMonthlyProfit = localStorage.getItem('monthlyProfit');
      if (savedMonthlyProfit) {
        this.monthlyProfit = parseFloat(savedMonthlyProfit);
      }
      const savedMonthlySpent = localStorage.getItem('monthlySpent');
      if (savedMonthlySpent) {
        this.monthlySpent = parseFloat(savedMonthlySpent);
      }
    }
  }
};
</script>

<style scoped>
@import url('assets/StylesApp.css');
  .remove-btn {
    background-color: transparent;
    width: 50px;
    border: none;
    cursor: pointer;
    margin-left: 10px;
  }
  .remove-btn:hover {
    background-color: #FF0000;
  }
  .remove-btn:hover svg {
    fill: white;
  }
</style>
