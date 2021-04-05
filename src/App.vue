<template>
  <div>
    <!-- When in naming State -->
    <div v-if="state === 'nameState'">
        <div class="learn-more">
          <button @click="startState(); " class="learn-more">start</button>
        </div>
    </div>

    <!-- When in running State -->
    <div v-if="state === 'runState'"> 
        <div>
            <timer @takeEnd="takeEnd" v-bind:start-app="startApp" />
        </div>
        <!-- display Real text -->
        <div v-bind:style="styleObject">
          
            <div id="para" style="display: inline; top: 0px; width: 100% ;  "></div>
        </div>
        <!-- display Temp text -->
        <div style="text-align: center; position:relative" >
            <div v-if="!isPlay">
              <p id="plus" style="display: inline ; top: 0px; width: 100%"></p>
            </div>
            <div id="tempText" style="display: inline ; top: 0px; width: 100%"></div> 
        </div>
        
        
        
        <div v-if="isRecord && !isPlay" style="position: relative; margin: 0 auto; text-align: center">
            <img class = "image-style" src="https://www.flaticon.com/svg/vstatic/svg/945/945185.svg?token=exp=1617564095~hmac=14fe0a401d86eaa536b3213040afa148" >
        </div>
    </div>

    <!-- When in end State -->
    <div v-if="state ==='endState'" >
        <p class="popout">
          <span>E</span><span>N</span><span>D</span><span>I</span><span>N</span><span>G</span>
        </p>
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
      state: "nameState",
      myTrack: new Audio(),
      letter: json.letters,     //Import json file
      i: 0,                     //This integer for what number of alphabet in word              
      j: 0,                     //This integer for what number of word
      previusJ:0,
      thisString: [],           //this is a main string of word
      coloredString: [],        //this is a color string of word
      isStop: false,            //this boolean will turn to true when finish render 1 word
      isPlay: false,            //this boolean will turn to false when user press keyboard   
      isRecord: false,
      isEnd: false,
      arrayPool: [],            //Use to collect rendered word
      startApp:false,           //Boolean for sent data back to timer
      currentString: "",
      //Text style 
      fontSize:400,             //text size
      styleObject: {
        top: '50px',
        color: 'blue',
        position: 'fixed',
        left: '10px', 
      },
    };
  },

  methods: {
    takeEnd(value){
        this.isEnd = value;
        this.state = "endState"
    },
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
            "<strong  style='font-size:"+(this.percent(this.fontSize , 100.5 )) +"px; opacity:0% ; font-weight: 401 !important; color: " +
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
    runningFunction(){
      if(!this.startApp) this.startApp = true //To return that game start
            if(this.isRecord === true){
              this.isRecord = false;
            }
            if(!this.isPlay){
              var para = document.getElementById("plus");
              var para2 = document.getElementById("para");
              var para3 = document.getElementById("tempText");
              this.thisString = []  
              this.coloredString= [],
              para.innerHTML = "<p style='font-size:"+ (this.percent(this.fontSize, 120))+"px ;color:black ;'>+</p>";
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
    },
    turnRecord(){
      if(!this.isPlay && this.isRecord === false){
            this.isRecord = true;
        }
    },
    startState(){
      this.state = 'runState' ; 
      setTimeout(()=>{
            this.turnRecord() ; 
            this.runningFunction();
            },100)
      
    }

  },
  mounted: function() {

    //When app in name state
    window.addEventListener("keypress", (event) => {
      console.log(this.state)
      if(this.state === 'nameState'){

      }
      else if(this.state === 'runState'){
        if(event.code === 'Space'){
          this.turnRecord()
        } 
      }
      else if(this.state === 'endState'){

      }

    });
    
      //add event when user interact with keyboard
    window.addEventListener("keyup", (event) => {
      console.log(event.code)
      //When in nameState
      if(this.state === 'nameState'){

      }
      //When in runningState
      else if(this.state === 'runState' ){     
                        
          if(event.code === 'KeyR'){
            if(!this.isPlay){      
              this.j = this.previusJ
              this.playSound();
            }
          }
          if(event.code === 'Space' ){
            this.runningFunction();
          }
      }
      //When in endState
      else if(this.state === 'endState'){

      }
    });
    
    
  },
};
</script>

<style >
@import url('https://fonts.googleapis.com/css2?family=Sarabun&display=swap');
.image_style{
  position: fixed;
  width: 20%;
  display: block;
  margin-left: auto;
  margin-right: auto;
  color: rebeccapurple;
  
}

* {
  padding: 0;
  margin: 0;
  
  font-family: 'Sarabun', sans-serif;
}

.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 1080px;
  border: 3px solid green; 
}

.image-style{
  position: fixed;
  top: 85%;
  left: 50%;
  width: 10%;
  height: auto;
  transform: translate(-50%, -50%);
}

b{
  font-weight: 399 !important;
}

/* For animation button */
@import url('https://fonts.googleapis.com/css?family=Rubik:700&display=swap');
div.learn-more {
	 font-family: 'Rubik', sans-serif;
	 font-size: 1rem;
	 line-height: 1.5;
	 display: flex;
	 align-items: center;
	 justify-content: center;
	 margin: 0;
	 min-height: 100vh;
	 background: #fff;
}
 button {
	 position: relative;
	 display: inline-block;
	 cursor: pointer;
	 outline: none;
	 border: 0;
	 vertical-align: middle;
	 text-decoration: none;
	 font-size: inherit;
	 font-family: inherit;
}
 button.learn-more {
   font-size: 100px;
	 font-weight: 600;
	 color: #382b22;
	 text-transform: uppercase;
	 padding: 1.25em 2em;
	 background: #fff0f0;
	 border: 2px solid #b18597;
	 border-radius: 0.75em;
	 transform-style: preserve-3d;
	 transition: transform 150ms cubic-bezier(0, 0, 0.58, 1), background 150ms cubic-bezier(0, 0, 0.58, 1);
}
 button.learn-more::before {
	 position: absolute;
	 content: '';
	 width: 100%;
	 height: 100%;
	 top: 0;
	 left: 0;
	 right: 0;
	 bottom: 0;
	 background: #f9c4d2;
	 border-radius: inherit;
	 box-shadow: 0 0 0 2px #b18597, 0 0.625em 0 0 #ffe3e2;
	 transform: translate3d(0, 0.75em, -1em);
	 transition: transform 150ms cubic-bezier(0, 0, 0.58, 1), box-shadow 150ms cubic-bezier(0, 0, 0.58, 1);
}
 button.learn-more:hover {
	 background: #ffe9e9;
	 transform: translate(0, 0.25em);
}
 button.learn-more:hover::before {
	 box-shadow: 0 0 0 2px #b18597, 0 0.5em 0 0 #ffe3e2;
	 transform: translate3d(0, 0.5em, -1em);
}
 button.learn-more:active {
	 background: #ffe9e9;
	 transform: translate(0em, 0.75em);
}
 button.learn-more:active::before {
	 box-shadow: 0 0 0 2px #b18597, 0 0 #ffe3e2;
	 transform: translate3d(0, 0, -1em);
}

/* for animation ending */
.popout {
  text-align: center;
	 font-family: Futura, sans-serif;
	 font-weight: 900;
	 font-size: 350px;
	 padding: 80px;
}
 @keyframes ani {
	 0% {
		 transform: translate3d(0, 0, 0);
		 text-shadow: 0em 0em 0 lightblue;
		 color: black;
	}
	 30% {
		 transform: translate3d(0, 0, 0);
		 text-shadow: 0em 0em 0 lightblue;
		 color: black;
	}
	 70% {
		 transform: translate3d(0.08em, -0.08em, 0);
		 text-shadow: -0.08em 0.08em lightblue;
		 color: black;
	}
	 100% {
		 transform: translate3d(0.08em, -0.08em, 0);
		 text-shadow: -0.08em 0.08em lightblue;
		 color: black;
	}
}
 .popout span {
	 position: relative;
	 display: inline-block;
	 animation: ani 1s infinite alternate cubic-bezier(0.86, 0, 0.07, 1);
}
 .popout span:nth-last-child(1n) {
	 animation-delay: -0.1666666667s;
}
 .popout span:nth-last-child(2n) {
	 animation-delay: -0.3333333333s;
}
 .popout span:nth-last-child(3n) {
	 animation-delay: -0.5s;
}

</style>
