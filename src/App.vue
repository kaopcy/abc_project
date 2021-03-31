<template>
  <div>
    <!-- display timer -->
    <div>
      <timer v-bind:start-app="startApp" />
    </div>
    <!-- display Real text -->
    <div v-bind:style="styleObject">
      <p id="para" style="display: inline; "></p>
    </div>
    <!-- display Temp text -->
    <div  style="text-align: center;" >
      <p id="para2" style="display: inline; "></p> 
    </div>

  </div>
</template>

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
      letter: json.letters,     //Import json file
      i: 0,                     //This integer for what number of alphabet in word              
      j: 0,                     //This integer for what number of word
      thisString: [],           //this is a main string of word
      coloredString: [],        //this is a color string of word
      isStop: false,            //this boolean will turn to true when finish render 1 word
      isPlay: false,            //this boolean will turn to false when user press keyboard   
      arrayPool: [],            //Use to collect rendered word
      startApp:false,           //Boolean for sent data back to timer
      
      //Text style 
      fontSize:350,             //text size
      styleObject: {
        top: '116px',
        color: 'blue',
        position: 'fixed',
        left: '666px',
      },
    };
  },

  methods: {
    //To get random number from word pool
    random(min,max,arrayPool) {
    var i = Math.floor(Math.random()*(max-min+1))+min;
      for (let j = 0 ; j < arrayPool.length ; ){
          if(i === arrayPool[j]){
              i = Math.floor(Math.random()*(max-min+1))+min;
              j = 0;
          }
          else{
              j++;
          }
          
      }
      arrayPool.push(i);
      return i;
    },
    // calculate percentage
    percent(input , percent){
      return input*percent/100
    },
    //Core function
    playSound() {
      this.makeString();
      if (this.letter[this.j][this.i].source !== "") {            //If there have source in file
        this.isPlay = true;
        this.isStop = false;
        this.myTrack = new Audio();
        this.myTrack.src = this.letter[this.j][this.i].source;    //path
        this.myTrack.play();
        this.myTrack.addEventListener(                            //when *1* alphabet ended
          "ended",
          function() {
            this.i++;
            if (this.i > this.letter[this.j].length - 1) {        //If end of 1 word 
              this.i = 0;
              this.thisString = [];                               //Reset both string and color
              this.coloredString = [];
              this.isPlay = false;
              this.isStop = true;
            }
            if (!this.isStop) {
              setTimeout(()=>{
                this.playSound();
              },100)
            }
          }.bind(this)
        );
      } 
      else {
        this.i++;
        if (!this.isStop) this.playSound();
      }
    },
    
    continuePlay() {
      if (this.isStop) {
        this.isStop = false;
      }
    },
    makeString: function() {
      var para = document.getElementById("para");
      var para2 = document.getElementById("para2");
      var invisString = [];
      var lastInvis = [];

      for(let i = 0 ; i< this.letter[this.j].length ; i++){
        if (this.letter[this.j][i].swap === "0") {
          if (this.letter[this.j][i].char !== "") {
            invisString.push(this.letter[this.j][i].char);
          }
        } 
        else if (this.letter[this.j][i].swap !== "0") {
          if (this.letter[this.j][i].char !== "") {
            invisString = this.swap(
              invisString,
              this.letter[this.j][i].char,
              this.letter[this.j][i].swap
            );
            
          }
        }
      }
      for (let index = 0; index < invisString.length; index++) {
        
          let lastInvis1 =
            "<strong  style='font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; opacity:0% ; font-weight: 401 !important; color: " +
            this.coloredString[index] +
            ";'>" +
            invisString[index] +
            "</strong>";
          lastInvis.push(lastInvis1);
        
      }

      para2.innerHTML = lastInvis.join("")


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
      
      console.log("This text top = "+para2.offsetTop)
      console.log("This text left = "+para2.offsetLeft)
      this.styleObject.left = para2.offsetLeft + "px"
    },
    swap(thisstring, character, num) {
      thisstring.splice(thisstring.length - num, 0, character);
      return thisstring;
    },
  },
  mounted: function() {
    this.i = 0;
    window.addEventListener("keypress", () => {
      if(!this.startApp) this.startApp = true
      var para = document.getElementById("para");
      this.thisString = []  
      this.coloredString= [],
      para.innerHTML = "<p style='font-size:"+ this.fontSize+"px ;color:red ;  text-align: center '>+</p>";
      setTimeout(()=>{
        
      if (!this.isPlay) {
        this.j= this.random(0,this.letter.length,this.arrayPool)
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
div.text_position{
  position:static;
  text-align: center;
  font-size: 400px;
  
}
</style>
