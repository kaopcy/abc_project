<template>
  <div>
    <!-- display timer -->
    <div>
      <timer v-bind:start-app="startApp" />
    </div>
    <!-- display Real text -->
    <div v-bind:style="styleObject">
      <div id="para" style="display: inline; top: 0px; width: 100% ;  "></div>
    </div>
    <!-- display Temp text -->
    <div  style="text-align: center;" >
      <div id="tempText" style="display: inline ; top: 0px; width: 100%"></div> 
    </div>
    
    <div v-if="!isPlay">
      <p id="plus"></p>
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
      previusJ:0,
      thisString: [],           //this is a main string of word
      coloredString: [],        //this is a color string of word
      isStop: false,            //this boolean will turn to true when finish render 1 word
      isPlay: false,            //this boolean will turn to false when user press keyboard   
      arrayPool: [],            //Use to collect rendered word
      startApp:false,           //Boolean for sent data back to timer
      currentString: "",
      //Text style 
      fontSize:400,             //text size
      styleObject: {
        top: '200px',
        color: 'blue',
        position: 'fixed',
        left: '10px', 
      },
    };
  },

  methods: {
    //To get random number from word pool
    random(min,max,arrayPool) {
    var i = Math.floor(Math.random()*(max-min))+min;
      for (let j = 0 ; j < arrayPool.length ; ){
          if(i === arrayPool[j]){
              i = Math.floor(Math.random()*(max-min))+min;
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
              console.log(this.currentString)
              console.log(this.arrayPool)
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
    makeTempString(){
      var tempText = document.getElementById("tempText");
      var invisString = [];
      var lastInvis = [];
      //******This for invisiblestring*******
      for(let i = 0 ; i< this.letter[this.j].length ; i++){     //making array of invisible string
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
      for (let index = 0; index < invisString.length; index++) {    //makeing array of html text 
        
          let lastInvis1 =
            "<strong  style='font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; opacity:2% ; font-weight: 401 !important; color: " +
            this.coloredString[index] +
            ";'>" +
            invisString[index] +
            "</strong>";
          lastInvis.push(lastInvis1);
        
      }
      tempText.innerHTML = lastInvis.join("")
      this.currentString = invisString.join("")
        this.styleObject.left = tempText.offsetLeft + "px"
        

    },
    
    //main fucntion for makeing string
    makeString: function() {
      var para = document.getElementById("para");
      
      //******This for RealString*******
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
              "<b style='font-size:"+(this.percent(this.fontSize , 96.25 )) +"px; color: " +
              this.coloredString[index] +
              "; font-weight: 389 ;'>" +
              this.thisString[index] +
              "</b>";
              lastString.push(lastStrings);
            }
          
        } 
        else if (this.coloredString[index] == "green") {
          if( this.thisString[index] === 'ุ'             //สระอุ
          ||  this.thisString[index] === 'ู')             //สระอุ
          {
            let lastStrings =
              "<span style='font-size:"+(this.percent(this.fontSize , 75 )) +"px; color: " +
              this.coloredString[index] +
              ";'>" +
              this.thisString[index] +
              "</span>";
            lastString.push(lastStrings);
          }
          else{
            let lastStrings =
              "<b style='letterSpacing:100px; font-size:"+(this.percent(this.fontSize , 100 )) +"px; color: " +
              this.coloredString[index] +
              ";'>" +
              this.thisString[index] +
              "</b>";
            lastString.push(lastStrings);
          }
        } 
        else {
          if(this.coloredString[index-1] !== "blue"){
            let lastStrings =
              "<strong  style='letterSpacing:100px; font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; font-weight: 401 !important; color: " +
              this.coloredString[index] +
              ";'>" +
              this.thisString[index] +
              "</strong>";
            lastString.push(lastStrings);
          }
          else{
            let lastStrings =
              "<strong :class='strongID' style='font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; font-weight: 401 !important; color: " +
              this.coloredString[index] +
              ";'>" +
              this.thisString[index] +
              "</strong>";
            lastString.push(lastStrings);
            
          }
        }
      }
      para.innerHTML = lastString.join("");
    },
    //this function use to swap array of string
    swap(thisstring, character, num) {
      thisstring.splice(thisstring.length - num, 0, character);
      return thisstring;
    },

    

  },
  mounted: function() {
    //add event when user interact with keyboard
      window.addEventListener("keyup", (event) => {
        console.log(event.code)
        if(event.code === 'KeyR'){
          if(!this.isPlay){      
            this.j = this.previusJ
            this.playSound();
          }
        }
        if(event.code === 'Space'){
          if(!this.startApp) this.startApp = true //To return that game start
          if(!this.isPlay){
            var para = document.getElementById("plus");
            var para2 = document.getElementById("para");
            var para3 = document.getElementById("tempText");
            this.thisString = []  
            this.coloredString= [],
            para.innerHTML = "<p style='font-size:"+ (this.percent(this.fontSize, 150))+"px ;color:black ;  text-align: center '>+</p>";
            para2.innerHTML = "";
            para3.innerHTML = "";
            this.j= this.random(0,this.letter.length,this.arrayPool)
            this.previusJ = this.j
          setTimeout(()=>{
          if (!this.isPlay) {
              this.makeTempString()
              this.playSound();
              
            }
          },2000)
          }
        }
        
    });
  },
};
</script>

<style >
@import url('https://fonts.googleapis.com/css2?family=Sarabun&display=swap');
.image_style{
  position: relative;
  z-index: -900;
  width: 5%;
  display: block;
  margin-left: auto;
  margin-right: auto;
  
}

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

.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 1080px;
  border: 3px solid green; 
}

strong{
  
}

b{
  font-weight: 399 !important;
}

</style>
