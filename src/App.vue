<template>
 <Header />
 <div class="container">
  <Balance :total="+total" />
  <IncomeExpenses :income="+income" :expenses="+expenses" />
  <TransactionList @transactionDeleted="handleTransactionDeleted" :transactions="transactions" />
  <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
 </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';
import { ref, computed, onMounted } from 'vue';

const toast = useToast();
const transactions = ref([]);

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransaction) {
    transactions.value = savedTransaction;
  }
})

// Get Total
const total = computed(() => {
  return transactions.value.reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0)
})

// Get Income
const income = computed(() => {
  return transactions.value
  .filter(transaction => transaction.amount > 0)
  .reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0)
  .toFixed(2);
})

// Get Expenses
const expenses = computed(() => {
  return transactions.value
  .filter(transaction => transaction.amount < 0)
  .reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0)
  .toFixed(2);
})

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionsToLocalStorage();
  toast.success('Transaction added successfully');
}

// Generate id
const generateUniqueId = () => {
 return Math.floor(Math.random() * 1000000); 
}

// Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  saveTransactionsToLocalStorage();
  toast.error('Transaction deleted successfully');
}

// Save to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>