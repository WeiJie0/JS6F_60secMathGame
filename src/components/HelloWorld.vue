<template>
  <div>
    <div class="wrap bg-primary">
      <div class="container">
        <div class="start" v-if="status ===  'start'">
          <div
            class="title d-flex justify-content-center align-items-center pt-5 animated fadeInLeft fast"
          >
            <div class="titleNumber text-white mr-4">60</div>
            <div class="titleEnglish text-black mt-3">
              <p>
                SECONDS
                <span class="symbol text-white">+−×÷</span>
              </p>
              <p>CHALLENGE</p>
            </div>
          </div>
          <div class="startbtn d-flex justify-content-center">
            <button
              class="btn btn-dark text-primary align-middle py-1 animated fadeInRight"
              @click.prevent="playGame()"
            >START!</button>
          </div>
          <div class="startDescription d-flex justify-content-center animated fadeInRight">
            <small class="text-white">try to answer more as you can</small>
          </div>
        </div>
        <div class="game" v-if="status ===  'game'">
          <div class="prompt d-flex justify-content-center align-items-center pt-6 mb-7">
            <div class="d-flex flex-column mr-6">
              <div class="littleTitle text-white">60 SECONDS CHALLENGE</div>
              <div class="scorebox d-flex">
                <div
                  class="scoreTitle text-primary bg-white d-flex justify-content-center align-items-center"
                >SCORE</div>
                <div class="score text-black ml-2">{{score}}</div>
              </div>
            </div>

            <div class="time text-white" v-if="time>=10">00:{{time}}</div>
            <div class="time text-white" v-else>00:0{{time}}</div>
          </div>
          <div class="question d-flex justify-content-center align-items-center">
            <span class="num1 text-black">{{num1}}</span>
            <span class="operand text-white">{{symbol}}</span>
            <span class="num2 text-black">{{num2}}</span>
            <span class="equal text-white">=</span>
            <div class="ansbox d-flex flex-column">
              <input
                v-focus
                class="ans ml-4 border-0"
                type="tel"
                name="ans"
                id="ans"
                v-model="inputAns"
                v-on:keyup.enter="sumit(inputAns)"
              >
              <small class="text-white ml-3">press enter to answer</small>
            </div>
          </div>
        </div>
        <div class="end" v-if="status ===  'end'">
          <div class="title d-flex flex-column justify-content-center align-items-center pt-6">
            <div class="littleTitle text-white">60 SECONDS CHALLENGE</div>
            <div class="getScoreTitle">
              <span class="text-white">—</span> YOUR FINAL SCORE
              <span class="text-white">—</span>
            </div>
            <div class="getScore animated fadeIn">{{score}}</div>

          </div>

          <div class="startbtn d-flex justify-content-center mt-6">
            <div
              class="btn btn-dark text-primary align-middle py-1"
              @click.prevent="playGame()"
            >TRY AGAIN!</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      status: "start",
      time: 60,
      score: 0,
      num1: 0,
      num2: 0,
      symbol: "",
      operand: "",
      ans: 0,
      inputAns: "",
    };
  },
  methods: {
    playGame() {
      var vm = this;
      vm.score = 0;
      vm.time = 60;
      vm.status = "game";
      vm.setTime();
      vm.getQuestion();
      vm.calc();
    },
    setTime() {
      var vm = this;
      var interval = setInterval(function() {
        vm.time = vm.time - 1;
        if (vm.time === 0) {
          clearInterval(interval);
          vm.status = "end";
        }
      }, 1000);
    },
    getQuestion() {
      var vm = this;
      const getSymbol = () => {
        var num = Math.floor(Math.random() * 4); 
        switch (true) {
          case num === 0:
            vm.symbol = "+";
            vm.break;
          case num === 1:
            vm.symbol = "-";
            break;
          case num === 2:
            vm.symbol = "×";
            break;
          case num === 3:
            vm.symbol = "÷";
        }
      }; //隨機得到加減乘除
      getSymbol();
      switch (true) {
        case vm.time <= 20:
          vm.num1 = Math.floor(Math.random() * 999);
          vm.num2 = Math.floor(Math.random() * 999);
          break;
        case vm.time <= 40:
          vm.num1 = Math.floor(Math.random() * 99);
          vm.num2 = Math.floor(Math.random() * 99);
          break;
        case vm.time > 41:
          vm.num1 = Math.floor(Math.random() * 9);
          vm.num2 = Math.floor(Math.random() * 9);
          break;
      } //隨機得到數字
    },
    calc() {
      var vm = this;
      switch (vm.symbol) {
        case "+":
          vm.ans = vm.num1 + vm.num2;
          break;
        case "-":
          vm.ans = vm.num1 - vm.num2;
          break;
        case "×":
          vm.ans = vm.num1 * vm.num2;
          break;
        case "÷":
          vm.ans = vm.num1 / vm.num2;
          break;
      }
      if (vm.symbol === "÷") {
        if (vm.num1 % vm.num2 != 0) {
          vm.getQuestion();
          vm.calc();
        }
      } // 若無法整除重新出題
      if (vm.ans <= 0) {
        vm.getQuestion();
        vm.calc();
      } // 若答案為負數重新出題
    },
    sumit(ans) {
      var vm = this;
      if (ans === "") {
        vm.inputAns = ""; // 防止未輸入即送出
      } else {
        if (vm.ans == ans) {
          if (vm.time > 41) {
            vm.score = vm.score + 1;
          } else {
            vm.score = vm.score + 5;
          }
          vm.inputAns = "";
          vm.getQuestion();
          vm.calc();
        } else {
          if (vm.score != 0) {
            vm.score = vm.score - 1;
          }
          vm.inputAns = "";
          vm.getQuestion();
          vm.calc();
        }
      }
    }
  },
  directives: {
    focus: {
      // 指令的定义
      inserted: function(el) {
        el.focus();
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url("https://fonts.googleapis.com/icon?family=Roboto+Condensed");
.wrap {
  height: 100vh;
  width: 100vw;
  font-family: "Roboto Condensed";
  font-weight: bolder;
}
.titleNumber {
  font-size: 240px;
  text-shadow: 12px 12px #000000;
}
.titleEnglish p {
  font-size: 96px;
  line-height: 96px;
  margin-bottom: -1rem;
}
.symbol {
  font-size: 44px;
  text-decoration-line: underline;
  border-bottom: 4px solid;
}
.startbtn .btn {
  height: 58px;
  width: 350px;
  font-size: 36px;
}
.startbtn :hover {
  font-size: 45px;
}
.startDescription small {
  font-size: 24px;
  font-style: italic;
}
.littleTitle {
  font-size: 30px;
  border: 4px #ffffff solid;
}
.time {
  font-size: 96px;
  font-style: italic;
}
.scoreTitle {
  font-size: 30px;
  height: 55px;
  width: 114px;
}
.score {
  font-size: 44px;
}
.question {
  font-size: 112px;
}
.ans {
  height: 131px;
  width: 255px;
  font-size: 112px;
  text-align: center;
}
.ansbox small {
  font-size: 24px;
  text-align: center;
  font-style: italic;
}
.getScoreTitle {
  font-size: 56px;
}
.getScore {
  font-size: 114px;
}
</style>
