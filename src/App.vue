<template>
  <div>
    <div>
      <timer />
      <button ><router-link to= '/table'>Click to go to table</router-link></button>
      <button ><router-link to= '/Home'>Click to go to Home</router-link></button>
      <router-view></router-view>
      <br>
      <button
        :class="!isPlay ? 'visible' : 'invisible'"
        @click="
          playSound();
          continuePlay();
        "
      >
        Click to play
      </button>
      <button @click="addData" class=" rounded-xl font-extrabold bg-yellow-700 place-items-center m-2 p-2 text-3xl text-white w-50  animate-bounce align-middle000">Click to add</button>
    </div>
      <li id="hehehe" >{{testedString}}</li>

    <div class="text_position" style="left: 30%;">

      <p id="para" style="display: inline ">{{ coloredString }}</p>
    </div>
    <!-- <span style='fontSize:9px  ; color: green;  '>ื</span>
    <sup style='fontSize:7px  ; color: blue;  '>้</sup> -->
    
  </div>
</template>

<style></style>

<script>
import json from "@/assets/letter/letter.json";
import timer from "@/components/layout/timer.vue";

export default {
  name: "app",
  components: {
    timer,
  },

  data() {
    return {
      myTrack: new Audio(),
      letter: json.letters,
      i: 0,
      j: 0,
      
      stop: false,
      thisString: [],
      coloredString: [],
      isPlay: false,
      currentSound: "",
      array: [],
      fontSize:350,

      testedString:"hi im ga"

    };
  },
  methods: {
    addData(){
      this.letter[0][0].char = 0
    },
    random(min,max,array) {
    var i = Math.floor(Math.random()*(max-min+1))+min;
      for (let j = 0 ; j < array.length ; ){
          if(i === array[j]){
              i = Math.floor(Math.random()*(max-min+1))+min;
              j = 0;
          }
          else{
              j++;
          }
          
      }
      array.push(i);
      return i;
    },
    percent(input , percent){
      return input*percent/100
    },
    playSound() {
      this.makeString();
      console.log(this.stop);
      if (this.letter[this.j][this.i].source !== "") {
        this.isPlay = true;
        this.myTrack = new Audio();
        this.myTrack.src = this.letter[this.j][this.i].source;
        this.myTrack.play();
        this.myTrack.addEventListener(
          "ended",
          function() {
            this.i++;
            if (this.i > this.letter[this.j].length - 1) {
              this.i = 0;
              
              this.thisString = [];
              this.coloredString = [];
              this.isPlay = false;
              this.stop = true;
            }
            if (!this.stop) {
              setTimeout(()=>{
                this.playSound();
              },100)
            }
          }.bind(this)
        );
      } else {
        this.i++;
        if (!this.stop) this.playSound();
      }
    },
    continuePlay() {
      if (this.stop) {
        this.stop = false;
      }
    },
    makeString: function() {
      var para = document.getElementById("para");
      var lastString = [];
      if (this.letter[this.j][this.i].swap === "0") {
        if (this.letter[this.j][this.i].char !== "") {
          this.thisString.push(this.letter[this.j][this.i].char);
          this.coloredString.push(this.letter[this.j][this.i].color);
        }
      } else if (this.letter[this.j][this.i].swap !== "0") {
        if (this.letter[this.j][this.i].char !== "") {
          this.thisString = this.swap(
            this.thisString,
            this.letter[this.j][this.i].char,
            this.letter[this.j][this.i].swap
          );
          this.coloredString = this.swap(
            this.coloredString,
            this.letter[this.j][this.i].color,
            this.letter[this.j][this.i].swap
          );
        }
      }
      for (let index = 0; index < this.thisString.length; index++) {
        if (this.coloredString[index] == "blue") {
            if(this.thisString[index-1] === 'ั' ||this.thisString[index-1] === 'ิ'||this.thisString[index-1] === 'ี'||this.thisString[index-1] === 'ึ'  || this.thisString[index-1] === 'ื'){
              console.log("in upper casestatement")
              let lastStrings =
              "<sup style='font-size:"+(this.percent(this.fontSize , 85 )) +"px; color: " +
              this.coloredString[index] +
              ";  '>" +
              this.thisString[index] +
              "</sup>";
              lastString.push(lastStrings);
            }
            else{
              let lastStrings =
              "<span style='font-size:"+(this.percent(this.fontSize , 96.25 )) +"px; color: " +
              this.coloredString[index] +
              "; font-weight: 389 ;'>" +
              this.thisString[index] +
              "</span>";
              lastString.push(lastStrings);
            }
          
        } else if (this.coloredString[index] == "green") {
          let lastStrings =
            "<span style='font-size:"+(this.percent(this.fontSize , 100 )) +"px; color: " +
            this.coloredString[index] +
            ";'>" +
            this.thisString[index] +
            "</span>";
          lastString.push(lastStrings);
        } else {
          let lastStrings =
            "<strong  style='font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; font-weight: 401 !important; color: " +
            this.coloredString[index] +
            ";'>" +
            this.thisString[index] +
            "</strong>";
          lastString.push(lastStrings);
        }
      }

      
      para.innerHTML = lastString.join("");
      var hehehe = document.getElementById("hehehe")
      console.log("This text width = "+hehehe.clientWidth)
      console.log("This text height = "+hehehe.clientHeight)
      console.log("This text top = "+hehehe.clientTop)
      console.log("This text left = "+hehehe.clientLeft)
    },
    swap(thisstring, character, num) {
      thisstring.splice(thisstring.length - num, 0, character);
      return thisstring;
    },
  },
  mounted: function() {
    this.i = 0;
    window.addEventListener("keypress", () => {
      var para = document.getElementById("para");
      this.thisString = []  
      this.coloredString= [],
      para.innerHTML = "<span style='font-size:600px ;color:red ; textAlign: center;' >+</span>";
      setTimeout(()=>{
        
      if (!this.isPlay) {
        this.j= this.random(0,this.letter.length,this.array)
          console.log("Okay this is a track that is currentluy played   "+this.j)
          this.playSound();
          this.continuePlay();
        }
      },2000)
    });
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Sarabun&display=swap');


* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  font-family: 'Sarabun', sans-serif;
}
.text_position{
  position:absolute;
  
}
</style>
