<template>
    <h3>Add new transaction</h3>
      <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
          <label for="text">Description</label>
          <input type="text" v-model="text" id="text" placeholder="Enter a description..." />
        </div>
        <div class="form-control">
          <label for="amount"
            >Amount <br />
            (<span style="color: red;">negative - expense</span>, <span style="color: green;">positive - income</span>)</label
          >
          <input type="text" v-model="amount" id="amount" placeholder="Enter amount..." />
        </div>
        <button class="btn">Add transaction</button>
      </form>
</template>

<script setup>
import { ref } from 'vue';
import {useToast} from 'vue-toastification'

const text = ref('');
const amount = ref('');

const emit = defineEmits(['transactionSubmitted'])

const toast = useToast();

const onSubmit = () => {
  if(!text.value || !amount.value){
    toast.error('Both fields must be filled');
    return;
  }
  const transactionData ={
    text: text.value,
    amount: parseFloat(amount.value)
  }

  emit('transactionSubmitted', transactionData);

  text.value = '';
  amount.value = '';
}


</script>