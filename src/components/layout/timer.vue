<template>
  <div id="timer">
    <span style="position: absolute;
    bottom: 0;
    left: 0;">Time {{displayMinute}}:{{displaySecond}} Difficult: {{difficult}}</span>
  </div>
</template>

<script>
export default {
  name: "timer",
  props:{
    startApp:Boolean,
  },
  data() {
    return {
      difficult: "easy",
      Minute: 0,
      Second: 15,
      displayMinute: "00",
      displaySecond: "00",
      test:true,
      isEnd: false,
      all:120,
      stage:0,
    };
  },
  methods: {},
  mounted() {
    this.stage = this.all / 3
    setInterval(() => {
      
      if(this.startApp ){
        if(this.all > 0){
          this.all-=1
          this.Second = this.all%60
          this.Minute = parseInt(this.all/60)
          if(this.Second < 10){
            this.displaySecond = "0"+this.Second
          }
          else{
            this.displayMinute = this.Minute
            this.displaySecond = this.Second
          }
        }
        else{
          this.isEnd = true;
          this.$emit("takeEnd",this.isEnd)
        }

        //Check for difficult
        if(this.all > this.stage * 2){
          this.difficult = 'easy';
          this.$emit('takeDifficult',this.difficult)
        }
        else if(this.all > this.stage && this.all <= this.stage * 2){
          this.difficult = 'normal';
          this.$emit('takeDifficult',this.difficult)
        }
        else{
          this.difficult = 'hard'
          this.$emit('takeDifficult',this.difficult)
        }
        

      }
    }, 1000);
  },
};
</script>

<style></style>
