<template> 
    <Header />
    <hr>
    <div class="container">
        <Balance :total="+total" />
        <IncomeExpense :income="+income" :expense="+expense" />
        <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
       <div class="center" v-if="isEmpty == true">
        <span style="color: red;">No data to display...</span>
       </div>
        <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
    </div>
</template>

<style>
    .center{
        display: flex;
        widows: 100%;
        justify-content: center;
    }
</style>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { ref, computed, onMounted } from 'vue';
import {useToast} from 'vue-toastification'

const toast = useToast();
let isEmpty = true;
const transactions = ref([]);

onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
    if (savedTransactions.length > 0) {
        transactions.value =  savedTransactions;
        isEmpty = false;
    }
});

//get total
const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0);
});

//get income
const income = computed(() => {
    return transactions.value
        .filter((t) => t.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0).toFixed(2);
});

//get expenses 
const expense = computed(() => {
    return transactions.value
        .filter((t) => t.amount < 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0).toFixed(2);
});

//add transaction 
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    });
    saveTransactionsToLocalStorage();
    isEmpty = false;
    toast.success('Transaction added!')
}

//generate id
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000)
}

//delete transaction 
const handleTransactionDeleted = (id) => {
   transactions.value = transactions.value.filter((t) => t.id !== id);
   if (transactions.length <= 0) {
        isEmpty = true;
   }
   saveTransactionsToLocalStorage();
   toast.success('transaction deleted!')
}

// save to local storage 
const saveTransactionsToLocalStorage = () =>{
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>