<template>
  <div>
    <button id="show-modal" @click="showModal = true">Show Modal</button>
    <transition v-show="showModal" @close="showModal = false" name="modal">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container">
            <div class="modal-header">
              <slot name="header"> default header </slot>
            </div>

            <div class="modal-body">
              <slot name="body">
                <div class="djpp">
                  <div class="video-content" v-html="content"></div>
                  <div class="video-wraper">
                    <div
                      v-show="showPointer"
                      @click="play"
                      :id="pointerID"
                      class="pointer"
                    >
                      <div class="hotspot"></div>
                      <div
                        v-if="showHelpText"
                        class="hotspot-text"
                        v-html="clickText"
                      ></div>
                    </div>
                    <video :id="playerID" class="player" muted>
                      <source src="video.mp4" type="video/mp4" />
                    </video>
                  </div>
                </div>
              </slot>
            </div>

            <div class="modal-footer">
              <slot name="footer">
                default footer
                <button class="modal-default-button" @click="restart">
                  OK
                </button>
              </slot>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pointerID: "pointer1",
      playerID: "player1",
      content: "No content found",
      showPointer: true,
      clickText: "Click the circle <br /> to continue",
      index: 0,
      showModal: false,
      showHelpText: true,
      results: [
        {
          id: "1",
          duration: 1,
          content: "First Content",
          top: 10,
          left: 20,
        },
        {
          id: "2",
          duration: 2,
          content: "Second Content",
          top: 50,
          left: 50,
        },
        {
          id: "3",
          duration: 3,
          content: "Thired content with duration 3",
          top: 10,
          left: 10,
        },
        {
          id: "4",
          duration: 3,
          content: "Fourth content with duration 3",
          top: 20,
          left: 20,
        },
      ],
    };
  },
  methods: {
    play() {
      if(this.index >= 0){
        this.showHelpText = false;
      }
      var player = document.getElementById(this.playerID);
      this.showPointer = false;
      this.index++;

      if (!this.results[this.index]) {
        player.play();
        setTimeout(() => {
          // player.pause();
        }, this.results[this.index - 1].duration * 1000);
        return;
      }
      console.log(this.index);

      player.play();
      let instruction = this.results[this.index];

      setTimeout(() => {
        player.pause();

        this.showPointer = true;
        var pointer = document.getElementById(this.pointerID);
        pointer.style.top = instruction.top + "%";
        pointer.style.left = instruction.left + "%";
        pointer.style.transform = ` translate(-${instruction.top}%, -${instruction.left}%) translate3d(0px, 0px, 0px) scale(1.6, 1.6)`;

        console.log("Position");
        console.log(instruction);

        this.content = instruction.content;
        if (this.showModal == false) {
          this.ready();
        }
        // if (this.results[this.index + 1]) {
        //   this.index++;
        // }else{
        //   this.showPointer = false;
        // }
      }, instruction.duration * 1000);
    },

    restart() {
      this.showModal = false;
      var player = document.getElementById(this.playerID);
      player.pause();
      player.currentTime = 0;
      this.index = 0;
      this.ready();
    },

    ready() {
      let instruction = this.results[this.index];

      var pointer = document.getElementById(this.pointerID);
      pointer.style.top = instruction.top + "%";
      pointer.style.left = instruction.left + "%";
      pointer.style.transform = ` translate(-${instruction.top}%, -${instruction.left}%) translate3d(0px, 0px, 0px) scale(1.6, 1.6)`;
      this.content = instruction.content;
      console.log("Position");
      console.log(instruction);
      console.log(this.index);
    },
  },
  mounted() {
    // window.onresize = () => {
    //   console.log(window.innerWidth)
    // }

    this.ready();
   
    // this.index++;

    // Video information

    var myVideoPlayer = document.getElementById(this.playerID);
    // meta = document.getElementById('meta');

    myVideoPlayer.addEventListener("loadedmetadata", function () {
      var duration = myVideoPlayer.duration;
      // console.log(myVideoPlayer);
      console.log("Duration is " + duration.toFixed(2) + " seconds.");
    });
  },
};
</script>

<style scoped>

video{
  border-radius: 5px;
}
.djpp {
  display: flex;
  align-items: center;
}
.pointer {
  display: flex;
  align-items: center;
  position: absolute;
  z-index: 999;
  cursor: pointer;
  /* top: 12%;
  left: 39%;
  transform: translate(-12%, -39%) translate3d(0px, 0px, 0px) scale(1.6, 1.6); */
}

.player {
  width: 100%;
}

.video-wraper {
  position: relative;
  width: 50%;
}

.video-content {
  position: relative;
  width: 50%;
}

@-webkit-keyframes ringPulse {
  0% {
    opacity: 0;
    transform: scale(1);
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: scale(1.8);
  }
}
@keyframes ringPulse {
  0% {
    opacity: 0;
    transform: scale(1);
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: scale(1.8);
  }
}

.hotspot {
  position: relative;
  background-color: yellow;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  cursor: pointer;
  /* opacity: 0.5; */
  z-index: 998;
  display: inline-block;
  margin: 0 20px;
}
.hotspot:before,
.hotspot:after {
  position: absolute;
}
.hotspot:before {
  content: "";
  border-radius: 50%;
  width: 23px;
  height: 23px;
  top: calc(-2px / 2);
  left: calc(-2px / 2);
  border: 2px solid yellow;
  transform-origin: 50%;
  transition: all 0.5s;
  -webkit-animation: ringPulse 4s infinite;
  animation: ringPulse 4s infinite;
}
.hotspot:after {
  background-color: #000;
}
.hotspot.active {
  opacity: 1;
  background-color: #f3ed8b;
}
.hotspot.active:before {
  border-color: #f3ed8b;
}
.hotspot.visited {
  opacity: 1;
}

.hotspot-text {
  color: white;
  border-radius: 5px;
  border: 1px solid gray;
  background: black;
  padding: 10px;
}

@media (max-width: 320px) {
  /* smartphones, portrait iPhone, portrait 480x320 phones (Android) */
}
@media (max-width: 480px) {
  /* smartphones, Android phones, landscape iPhone */
}
@media (min-width: 600px) {
  /* portrait tablets, portrait iPad, e-readers (Nook/Kindle), landscape 800x480 phones (Android) */
}
@media (max-width: 801px) {
  /* tablet, landscape iPad, lo-res laptops ands desktops */
  .video-content {
    width: 100%;
  }

  .video-wraper {
    width: 100%;
  }
  .djpp {
    flex-direction: column;
  }
}
@media (min-width: 1025px) {
  /* big landscape tablets, laptops, and desktops */
}
@media (min-width: 1281px) {
  /* hi-res laptops and desktops */
}

/* Modal */

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 90%;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
