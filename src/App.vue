<template>
  <Header />
  <div class="container">
    <Balance :total = "+total"/>
    <IncomeExpense :income = "income" :expense ="+expense" />
    <TransactionList :transactions = "transactions"  @deleteTransaction="handleTransactioDelete"/>
    <AddTransaction  @transactionSubmitted = "handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpense from './components/IncomeExpense.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { useToast } from 'vue-toastification';

const toast = useToast()

import { ref, computed, onMounted } from 'vue' 

const transactions = ref([])

onMounted(() => {
    const saveTransactions = JSON.parse(localStorage.getItem('transactions'))
    if (saveTransactions) {
        transactions.value = saveTransactions
    }
})
//Get total
const total = computed(() => {
    return transactions.value.reduce((acc, item) => (acc += item.amount), 0).toFixed(2)
})
//Get income
const income = computed(() => {
    return transactions.value
        .filter(item => item.amount > 0)
        .reduce((acc, item) => (acc += item.amount), 0)
        .toFixed(2)
})
//Get expense
const expense = computed(() => {
    return transactions.value
        .filter(item => item.amount < 0)
        .reduce((acc, item) => (acc += item.amount), 0) * -1
        .toFixed(2)
})  
//Handle transaction submitted
const handleTransactionSubmitted = (transactionsData) => {
    transactions.value.push({
        id: generateRandomId(),
        text: transactionsData.text,
        amount: transactionsData.amount
    })
    
    saveTransactionsToLocalStorage();

    toast.success('Transaction added successfully')
}
//Handle transaction delete
const handleTransactioDelete = (id) => {
    transactions.value = transactions.value.filter(transaction => transaction.id !== id)
    saveTransactionsToLocalStorage();
    toast.success('Transaction deleted successfully')
}
//Generater random id
const generateRandomId = () => {
    return Math.floor(Math.random() * 100000000)
}

// Save transactions to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>


