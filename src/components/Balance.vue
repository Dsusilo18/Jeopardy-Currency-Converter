<script>

import Slider from '@vueform/slider'
export default {
    name: 'Balance',
    props: ["amount", "convertFrom"],
    data() {
        return {
            balance: 100,                                                                                                                                   
            isDisabled: false
        }
    },
    computed: {
        balanceString() {
            return `Account Balance: ${this.balance}`;
        }
    },
    methods: {
        addBalance() {
            this.balance += this.amount
            //this.amount = 0;
        },
        subtractBalance() {
            this.balance -= this.amount
            //this.amount = 0;
        },
        disableBtn(){
          this.$emit("amountChange", this.amount);
          if(this.amount > this.balance){
            this.isDisabled = true;
          } else{
            this.isDisabled = false;
          }
        }
    },
    mounted() {
        console.log('Application mounted');
    },
    components: {
      Slider,
    }
}
</script>

<template>
    <div class="greetings">
      <h3>{{balanceString}} {{this.convertFrom}}, Amount: {{ amount }} {{this.convertFrom}}</h3>
      <p>Change amount to:<Slider v-model="amount"       
      :min="0"
      :max="100"
      :step="5"
      showTooltip="drag" @update="disableBtn"></Slider></p>
      <p class="btn"><button @click="addBalance">Add</button><button :disabled=this.isDisabled @click="subtractBalance">Subtract</button></p>
    </div>
</template>
  <style src="@vueform/slider/themes/default.css"></style>
  <style scoped>
  .btn {
    top: 10px;
  }
  
  h3 {
    font-size: 1.2rem;
    font-weight: 500;
    top: -10px;
  }
  
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
  
  @media (min-width: 1024px) {
    .greetings h1,
    .greetings h3 {
      text-align: left;
    }
  }
  </style>