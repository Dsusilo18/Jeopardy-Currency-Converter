<script>
  export default {
    mounted(){
      this.start();
    },
    data() {
        return {
            msgErr:'',
            numPlayers: 0,
            quest: "",
            ans: false,
            head: [],
            title: "",
            msgErr2:'',
            money: 0,
            flag: false,
            score: [],
            player:1,
            radFlag:false,
            questBackup: [],
            ansBackup: [],
            numQ: 0,
            modeBackup: []
        }
    },
    methods: {
      async getQuest(mode, genre, btn, cash){
        let proceed = true;
        if(this.flag){
            this.title = btn;
            this.money = cash;
            let m = '';
            if(mode == 1){
            m = 'easy';
            } else if(mode == 2){
                m = 'medium';
            } else{
                m = 'hard';
            }   
            
            let url = 'https://opentdb.com/api.php?amount=2&category=' + this.head[genre] + '&difficulty=' + m + '&type=boolean';
            let res = await fetch(url);
            let q = await res.json();
            if(q.results[0] == undefined){
              if(this.questBackup[genre]){
                this.quest = this.questBackup[genre];
                this.ans = this.ansBackup[genre];
                console.log(this.quest);
                console.log(this.ans);
              } else{
                document.getElementById("text4").innerHTML = "loading...";

                while(q.results[0] == undefined){
                    mode = this.getRand(3);
                    if(mode == 1){
                        m = 'hard';
                    } else if(mode == 2){
                        m = 'medium';
                    } else{
                        m = 'medium';
                    }
                    url = 'https://opentdb.com/api.php?amount=2&category=' + this.head[genre] + '&difficulty=' + m + '&type=boolean';
                    res = await fetch(url);
                    q = await res.json(); 
                }
                this.quest = q.results[0].question;
                this.ans = q.results[0].correct_answer;

                if(this.questBackup[genre]){
                  if(mode > this.modeBackup[genre]){
                    this.questBackup[genre] = q.results[1].question;
                    this.ansBackup[genre] = q.results[1].correct_answer;
                  }
                } else{
                  this.questBackup[genre] = q.results[1].question;
                  this.ansBackup[genre] = q.results[1].correct_answer;
                  this.modeBackup[genre] = mode;
                }
              }          
            } else{

              console.log(q);

              this.quest = q.results[0].question;
              this.ans = q.results[0].correct_answer;
              if(this.questBackup[genre]){
                if(mode > this.modeBackup[genre]){
                  this.questBackup[genre] = q.results[1].question;
                  this.ansBackup[genre] = q.results[1].correct_answer;
                }
              } else{
                this.questBackup[genre] = q.results[1].question;
                this.ansBackup[genre] = q.results[1].correct_answer;
                this.modeBackup[genre] = mode;
              }

            }
            
            this.print(genre);
            this.flag = false;
          }

      },
      print(genre){
          let n = genre;
          n++;
          document.getElementById("text4").innerHTML = "Player " + this.player + " selects " + document.getElementById("head" + n).innerText + " for $" + this.money +"00";
            const p = document.createElement("p");
            p.innerHTML = "Question: " + this.quest.toString();
            document.getElementById("text4").appendChild(p);
            this.radFlag = true;
          console.log(this.ans);
              
      },
      getRand(n){
          var randomNumber=Math.floor(Math.random()*(n));
          return randomNumber;
      },
      async getHeader(){
          this.flag = true;
          localStorage.clear();
          const url = 'https://opentdb.com/api_category.php';
          const res = await fetch(url);
          const data = await res.json();
          let n = 1;
          for(let i = 0; i < 6; i++){
              let num = this.getRand(24);
              if(localStorage.getItem(data.trivia_categories[num].id)){
                  while(localStorage.getItem(data.trivia_categories[num].id)){
                      num = this.getRand(24);
                  }
                  localStorage.setItem(data.trivia_categories[num].id, JSON.stringify(data.trivia_categories[num].name));
                  this.head.push(data.trivia_categories[num].id);
                  document.getElementById("head" + n).innerHTML = JSON.parse(localStorage.getItem(data.trivia_categories[num].id));
                  console.log("a");
              } else{
                  localStorage.setItem(data.trivia_categories[num].id, JSON.stringify(data.trivia_categories[num].name));
                  this.head.push(data.trivia_categories[num].id);
                  document.getElementById("head" + n).innerHTML = JSON.parse(localStorage.getItem(data.trivia_categories[num].id));
                  console.log("b");
              }
              n++;
          }
          console.log(this.head);
          console.log(localStorage);
          this.msgErr = "Its Player " + this.player + " turn!";
      },
      start(){
          console.log(this.amount);
          if(this.numPlayers != 0){
              localStorage.setItem("players", this.amount);
              this.numPlayers = 0;
              window.location.reload();
          } else{
          if(this.amount < 2 || this.amount == undefined){
              if(localStorage.getItem("players") && localStorage.getItem("players") > 1){
                  this.numPlayers = localStorage.getItem("players");
                  localStorage.clear();
                  this.printP();
              } else if(this.amount < 2){
                  this.msgErr = "Please insert a number of player bigger than 1";
                  localStorage.clear();
              }
          } else if(this.answer > 5){
              this.msgErr = "Please insert a number of player less than 6!";
          } else if(this.amount != undefined){
              if(localStorage.getItem("players")){
                  this.numPlayers = localStorage.getItem("players");
                  localStorage.clear();
              } else {
                  this.numPlayers = this.amount;
              }
              console.log("hi");
              this.printP();
            }
          }
        },
        printP(){
                this.amount = undefined;
                for( let i = 0; i < this.numPlayers; i++){
                    this.score.push(0);
                }
                this.getHeader();
        },
        submit(ansSub){
            console.log(ansSub);
            if(ansSub == this.ans){
                this.score[this.player - 1] += this.money * 100;
                const newItem = document.createElement('label');
                newItem.innerHTML = "P" + this.player;
                newItem.style.color = "green";
                document.getElementById(this.title).replaceWith(newItem);
                this.numQ++;

                if(this.numQ == 30){
                    this.getWin();
                }else{
                    this.msgErr = "Its still Player " + this.player + " turn!";
                    document.getElementById("text4").innerHTML = "";
                    this.radFlag = false;
                    this.flag = true;
                }

            } else{
                this.score[this.player - 1] -= this.money * 100;
                const newItem = document.createElement('label');
                newItem.innerHTML = "P" + this.player;
                newItem.style.color = "red";
                document.getElementById(this.title).replaceWith(newItem);

                if(this.player == this.numPlayers){
                    this.player = 1;
                } else{
                    this.player++;
                }
                this.numQ++;

                if(this.numQ == 30){
                    this.getWin();
                }else{
                    this.msgErr = "Its Player " + this.player + " turn!";
                    document.getElementById("text4").innerHTML ="";
                    this.radFlag = false;
                    this.flag = true;
                }
            }
            document.getElementById(ansSub).checked = false;
            console.log(this.numQ);
        },
        getWin(){
            let index = this.largestNum(this.score);
            index++;
            alert("Player " + index + " wins!!");
            window.location.reload();
        },
        largestNum(arr){        
            let ress = 0;        
            arr.sort((a, b) => a - b);       
            let l = 0, r = arr.length - 1;
            
            while (l < r) {
                let sum = arr[l] + arr[r];
                if (sum == 0) {
                    ress = Math.max(ress, Math.max(arr[l], arr[r]));
                    return ress;
                } else if (sum < 0) {
                    l++;
                } else {
                    r--;
                }
            }
            return ress;
            }

    }
}
</script>

<template>
  <body>
    <div>
    <h1><strong id = "title">
      Play Jeopardy!
      </strong>
    </h1>
    <p>
          <label for="uanswer" >Enter Number of Player:</label>
          <input type="number" ref ="answer" v-model.number = "amount" min = 2 max = 5 @change="start"/>
    </p>
    <p id = "text1" v-if="numPlayers != 0">
      Number of Players: {{numPlayers}};
      <ul>
        <li v-for="(value, key) in score" >
          Player {{key+1}}: ${{value}}
        </li>
      </ul>
    </p>
    <p v-if="msgErr != ''">
      {{msgErr}}
    </p>
    <p v-if="msgErr2 != ''">
      {{msgErr2}}
    </p>
        <table class="table table-hover row-clickable" bgcolor="grey">
            <tbody>
                <tr>
                    <td><a id = "head1" >Category 1</a></td>
                    <td><a id = "head2" >Category 2</a></td>
                    <td><a id = "head3" >Category 3</a></td>
                    <td><a id = "head4" >Category 4</a></td>
                    <td><a id = "head5" >Category 5</a></td>
                    <td><a id = "head6" >Category 6</a></td>
                </tr>
              <tr>
                <td><label id = "r1c1" @click="getQuest(1, 0, 'r1c1', 2)">$200</label></td>
                <td><label id = "r1c2" @click="getQuest(1, 1, 'r1c2', 2)">$200</label></td>
                <td><label id = "r1c3" @click="getQuest(1, 2, 'r1c3', 2)">$200</label></td>
                <td><label id = "r1c4" @click="getQuest(1, 3, 'r1c4', 2)">$200</label></td>
                <td><label id = "r1c5" @click="getQuest(1, 4, 'r1c5', 2)">$200</label></td>
                <td><label id = "r1c6" @click="getQuest(1, 5, 'r1c6', 2)">$200</label></td>
              </tr>
              <tr>
                <td><label id = "r2c1" @click="getQuest(1, 0, 'r2c1', 4)">$400</label></td>
                <td><label id = "r2c2" @click="getQuest(1, 1, 'r2c2', 4)">$400</label></td>
                <td><label id = "r2c3" @click="getQuest(1, 2, 'r2c3', 4)">$400</label></td>
                <td><label id = "r2c4" @click="getQuest(1, 3, 'r2c4', 4)">$400</label></td>
                <td><label id = "r2c5" @click="getQuest(1, 4, 'r2c5', 4)">$400</label></td>
                <td><label id = "r2c6" @click="getQuest(1, 5, 'r2c6', 4)">$400</label></td>
              </tr>
              <tr>
                <td><label id = "r3c1" @click="getQuest(2, 0, 'r3c1', 6)">$600</label></td>
                <td><label id = "r3c2" @click="getQuest(2, 1, 'r3c2', 6)">$600</label></td>
                <td><label id = "r3c3" @click="getQuest(2, 2, 'r3c3', 6)">$600</label></td>
                <td><label id = "r3c4" @click="getQuest(2, 3, 'r3c4', 6)">$600</label></td>
                <td><label id = "r3c5" @click="getQuest(2, 4, 'r3c5', 6)">$600</label></td>
                <td><label id = "r3c6" @click="getQuest(2, 5, 'r3c6', 6)">$600</label></td>
              </tr>
              <tr>
                <td><label id = "r4c1" @click="getQuest(2, 0, 'r4c1', 8)">$800</label></td>
                <td><label id = "r4c2" @click="getQuest(2, 1, 'r4c2', 8)">$800</label></td>
                <td><label id = "r4c3" @click="getQuest(2, 2, 'r4c3', 8)">$800</label></td>
                <td><label id = "r4c4" @click="getQuest(2, 3, 'r4c4', 8)">$800</label></td>
                <td><label id = "r4c5" @click="getQuest(2, 4, 'r4c5', 8)">$800</label></td>
                <td><label id = "r4c6" @click="getQuest(2, 5, 'r4c6', 8)">$800</label></td>
              </tr>
              <tr>
                <td><label id = "r5c1" @click="getQuest(3, 0, 'r5c1', 10)">$1000</label></td>
                <td><label id = "r5c2" @click="getQuest(3, 1, 'r5c2', 10)">$1000</label></td>
                <td><label id = "r5c3" @click="getQuest(3, 2, 'r5c3', 10)">$1000</label></td>
                <td><label id = "r5c4" @click="getQuest(3, 3, 'r5c4', 10)">$1000</label></td>
                <td><label id = "r5c5" @click="getQuest(3, 4, 'r5c5', 10)">$1000</label></td>
                <td><label id = "r5c6" @click="getQuest(3, 5, 'r5c6', 10)">$1000</label></td>
              </tr>
            </tbody>
          </table>   
    <div id = "text4">
    </div>
    <!-- <div v-if = "radFlag">
      <p>
        <input type="radio" id="True" name="ans_t" value="True" @change="submit('True')">
        <label for="true">True</label>
        <input type="radio" id="False" name="ans_f" value="False" @change="submit('False')">
        <label for="false">False</label>
      </p>
    </div> -->
    <div>
      <p v-if="radFlag">
        <input type="radio" id="True" name="ans_t" value="True" @change="submit('True')" >
        <label for="true">True</label>
        <input type="radio" id="False" name="ans_f" value="False" @change="submit('False')" >
        <label for="false">False</label>
      </p>
    </div>
    </div>
  </body>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  font-family:'Times New Roman', Times, serif;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
