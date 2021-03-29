<template>
  <div>
    <div>
      <timer />

      <button
        :class="!isPlay ? 'visible' : 'invisible'"
        @click="
          playSound();
          continuePlay();
        "
      >
        Click to play
      </button>
    </div>

    <div class="w-full h-screen flex flex-col justify-center items-cennter  ">
      <p id="para" class="logo-1  ">{{ coloredString }}</p>
    </div>
    <div style="fontSize:500px; color:red">hello my name is kao</div>
    
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
      j: 10,
      
      stop: false,
      thisString: [],
      coloredString: [],
      isPlay: false,
      currentSound: "",
      array: [],
    };
  },
  methods: {
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
              let lastStrings =
              "<sup style='fontSize:10px !important ; color: " +
              this.coloredString[index] +
              ";  '>" +
              this.thisString[index] +
              "</sup>";
              lastString.push(lastStrings);
            }
            else{
              let lastStrings =
              "<span style='color: " +
              this.coloredString[index] +
              ";'>" +
              this.thisString[index] +
              "</span>";
              lastString.push(lastStrings);
            }
          
        } else if (this.coloredString[index] == "green") {
          let lastStrings =
            "<span style='color: " +
            this.coloredString[index] +
            ";'>" +
            this.thisString[index] +
            "</span>";
          lastString.push(lastStrings);
        } else {
          let lastStrings =
            "<strong  style='font-weight: 401 !important;text-align:center; color: " +
            this.coloredString[index] +
            ";'>" +
            this.thisString[index] +
            "</strong>";
          lastString.push(lastStrings);
        }
      }

      console.log(this.coloredString);
      console.log(this.thisString);
      para.innerHTML = lastString.join("");
    },
    swap(thisstring, character, num) {
      thisstring.splice(thisstring.length - num, 0, character);
      return thisstring;
    },
  },
  mounted: function() {
    this.i = 0;
    window.addEventListener("keypress", () => {
      if (!this.isPlay) {
        this.j++
        console.log("Okay this is a track that is currentluy played   "+this.j)
        this.playSound();
        this.continuePlay();
      }
    });
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Sarabun&display=swap');



.error_template {
  background-color: gainsboro;
  color: darkmagenta;
  font-size: 50px;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}
.error_template2 {
  font-weight: 900;
  background-image: url(https://images2.alphacoders.com/994/thumb-1920-994016.png);
}
.content-wrapper {
  margin: 24px;
  padding: 24px;
  border-width: 3px;
  border-color: blanchedalmond;
  border-style: dashed;
}
:root {
  --shadow-color: rgba(255, 238, 0, 0.548);
  --shadow-color-light: white;
  color: lightseagreen;
}

* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  font-family: 'Sarabun', sans-serif;
}

body {
  font-family: "Archivo", sans-serif;
  background-color: #192824;
}

p {
  font-size: 150px;
  text-transform: uppercase;
  font-family: "Archivo Black", "Archivo", sans-serif;
  font-weight: normal;
  display: block;
}

sup {
    vertical-align: super;
    font-size: smaller;
}

.logo-1 {
  color: white;
  animation: neon 1s infinite;
  transition: 0.5s;
  margin: 0;
}

@keyframes neon {
 
}
</style>
