<script>
  import Balance from './components/Balance.vue'
  import HelloWorld from './components/Jeopardy.vue'
  import Currency from './components/Currency.vue'
  
  export default {
    data(){
      return{
        amount: 10,
        convertFrom: "INR"
      };
    },
    components: {
      Balance,
      HelloWorld,
      Currency
    },
    methods: {
      handleAmount(payload){
        this.amount = payload;
      },
      handleCurrency(payload){
        this.convertFrom = payload;
        console.log(this.convertFrom);
      }
    }
  }
  </script>
  
  <template>
    <header>
      <div class="wrapper">
        <HelloWorld />
        <v-spacer></v-spacer>
        <Balance @amountChange="handleAmount" :amount = "amount" :convertFrom="convertFrom"/>
        <v-spacer></v-spacer>
        <p class="curr">
        <Currency @amountChange="handleAmount" @currencyChange="handleCurrency" :amount = "amount"/>
        </p>
      </div>
    </header>
  </template>
  
  <style scoped>
  header {
    line-height: 1.5;
  }

  .curr {
    top: 20px;
  }
  </style>
  