<template>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
  <link rel="apple-touch-icon" href="logo.jpg"/>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <div class="dark-area">
    <div class="album-container">
      <div class="holder">
        <div id="photo-holder"></div>
      </div>
      <div id="preload"></div>
    </div>
  </div>
  <div class="input-container">
    <span class="icon-left"><i class="fas fa-link"></i></span>
    <input type="text" class="custom-input" placeholder="Enter URL" v-model="inputLink">
    <span class="icon-right" @click="add()"><i class="fas fa-plus"></i></span>
  </div>
  <!-- hey -->
</template>

<script>
// import { TweenMax, Power4, Elastic } from "gsap";
import { gsap } from 'gsap';
import { Power4, Elastic } from 'gsap/all';




export default {
  name: 'App',
  data(){
    return{
      inputLink:undefined,
      
      photoCount: 6,
    pieceCount: 6,
    onPhoto: -1,
    pieceCompleteCount: 0,
    delay: null,
    transitions: ['center', 'random'],
    transitionType: 0,
    // images: [], 
    // images: [new Image()],
    images: [

        { src: require('@/assets/images/music-guy.jpg'), loaded: false },
      ],

    }
  },
  methods:{
    add() {
        let img = new Image();
        img.src = this.inputLink;
        img.onload = () => {
          this.images.push(img);
          this.setup();
      };
    },

    // preload() {
    //   this.images[0].src = "./assets/images/music-guy.jpg";
    //   this.images[0].onload = () => {
    //     this.setup();
    //   };
    // },
    preload() {
      const image = new Image();
      image.src = this.images[0].src;

      image.onload = () => {
        this.images[0].loaded = true;
        // Perform any setup or logic that needs to happen after image preload here
        this.setup();
      };
    },

    setup() {
      let photoHolder = document.querySelector("#photo-holder");
      photoHolder.innerHTML = "";
      for (let i = 0; i < this.pieceCount; i++) {
        let newWidth = (((100 - (100 / this.pieceCount) * i)) / 100) * 100;
        let newBackgroundSize = 100 + (100 - newWidth) / newWidth * 100;
        let newTop = ((100 / this.pieceCount) * i) / 2;

        let section = document.createElement("div");
        section.className = "section";
        section.id = "piece" + i;
        section.style.top = newTop + "%";
        section.style.left = newTop + "%";
        section.style.width = newWidth + "%";
        section.style.height = newWidth + "%";
        section.style.backgroundSize = newBackgroundSize + "%";
        // section.style.backgroundImage = 'url("' + this.images[this.onPhoto].src + '")';
        ++this.onPhoto;
        if (this.onPhoto >= this.images.length) {
          this.onPhoto = 0;
        }
        section.style.backgroundImage = 'url("' + this.images[this.onPhoto].src + '")';

        photoHolder.appendChild(section);
      }
      this.nextSlide();
    },
    nextSlide() {
      clearInterval(this.delay);
      this.pieceCompleteCount = 0;
      ++this.onPhoto;
      if (this.onPhoto >= this.photoCount) {
        this.onPhoto = 0;
      }

      for (let i = 0; i < this.pieceCount; i++) {
        let spinDelay = 0;
        let spin = 360;
        let piece = document.querySelector("#piece" + i);

        switch (this.transitions[this.transitionType]) {
          case "random":
            spinDelay = Math.random() / 2;
            spin = Math.random() * 360;
            break;
          case "center":
            spinDelay = (this.pieceCount - i) / 10;
            spin = 181;
            break;
        }

        gsap.to(piece, 1, {
          delay: spinDelay,
          rotation: spin + '_long',
          onComplete: this.completeRotation,
          onCompleteParams: [piece],
          ease: Power4.easeIn,
        });
      }
    },
    completeRotation(piece) {
      // piece.style.backgroundImage = 'url("' + this.images[0].src + '")';
      piece.style.backgroundImage = 'url("' + this.images[this.images.length - 1].src + '")';

      gsap.to(piece, 2, {
        rotation: '0_short',
        onComplete: this.finishPieceanimation,
        ease: Elastic.easeOut,
      });
    },
    finishPieceanimation() {
      ++this.pieceCompleteCount;
      if (this.pieceCompleteCount == this.pieceCount) {
        // No more automatic rotation
      }
    },

  },
  mounted() {
    this.preload();
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.dark-area{
  position: absolute;
  display: block;
  top: 0;
  left: 0;
  background-color: #0B132B;
  width: 100%;
  height: 60vh;
  border-bottom-left-radius: 25vw;
  border-bottom-right-radius: 25vw;

}

.album-container{
  /* position: absolute;
  display: block;
  bottom: 0;
  left: 50%;
  transform: translate(-50%,50%);
  background-color: lightgrey;
  border-radius: 50%;
  width: calc(100% * 1/3);
  aspect-ratio: 1/1; */
  border: 3px solid white;
}


.input-container {
  position: relative;
  display: block;
  position: absolute;
  bottom: 10vh;
  left: 50%;
  transform: translateX(-50%);
  /* width: 90vw; */
  /* overflow: hidden; */
  margin: auto;
  /* background-color: red; */
  
}

.custom-input {
  /* width: 100%; */
  padding: 10px 50px;
  border: 2px solid #ccc;
  border-radius: 20px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.3s;
}

.custom-input:focus {
  /* border-color: #3A506B; */
}

.icon-left,
.icon-right {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 20px;
  height: 20px;
  text-align: center;
}

.icon-left {
  left: 15px;
}

.icon-right {
  right: 15px;
}

/* Adjust icon size and color */
.icon-left i,
.icon-right i {
  font-size: 18px;
  color: #3A506B;
}






#preload {
  display: none;
}

.album-container {
  /* width: 300px;
  height: 300px;
  border: 5px solid #ffffff;
  margin: 40px auto;
  position: relative;
  background-color: #ffffff;
  border-radius: 50%;
  box-shadow: 0 0 15px black; */

  position: absolute;
  display: block;
  bottom: 0;
  left: 50%;
  transform: translate(-50%,50%);
  background-color: lightgrey;
  border-radius: 50%;
  width: calc(100% * 2/5);
  aspect-ratio: 1/1;
}

#photo-holder {
  width: 100%;
  height: 100%;
  position: absolute;
  /* border: 3px solid white;
  border-radius: 50%; */
}

.section {
  background-repeat: no-repeat;
  background-position: center center;
  overflow: hidden;
  border-radius: 50%;
  position: absolute;
}
</style>
