<template>
  <div>
    <div class="led-row">
      <span class="led" :class="{ on: turnedOn }"></span>
    </div>
    <div class="button-row">
      <button @click="toggle"></button>
    </div>
  </div>
</template>

<script>
import "../../lib/webaudio-controls";
import ctx from "../../web-audio/audio-context.js";

export default {
  props: {
    turnedOn: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      switchOnBuffer: null,
      switchOffBuffer: null,
    };
  },
  methods: {
    toggle() {
      this.$emit("toggle");
      this.playSwitchOn();
    },
    playSwitchOn() {
      let node = ctx.createBufferSource();
      node.buffer = this.switchOnBuffer;
      node.connect(ctx.destination);
      ctx.resume();
      node.start();
    },
  },
  created() {
    fetch("audio/switch-on.mp3")
      .then((response) => response.arrayBuffer())
      .then((arrayBuffer) => ctx.decodeAudioData(arrayBuffer))
      .then((audioBuffer) => {
        this.switchOnBuffer = audioBuffer;
      });

    // fetch("audio/switch-off.mp3")
    //   .then((response) => response.arrayBuffer())
    //   .then((arrayBuffer) => ctx.decodeAudioData(arrayBuffer))
    //   .then((audioBuffer) => {
    //     this.switchOffBuffer = audioBuffer;
    //   });
  },
};
</script>

<style lang="scss" scoped>
.led {
  &.on {
    background-color: red;
  }
}
.button-row {
  text-align: center;
}

.led {
  display: inline-block;
  height: 12px;
  border-radius: 6px;
  width: 12px;
  background-image: url("~@/assets/img/led.png");
  background-position-x: -6px;
  background-position-y: -6px;
}
.led.on {
  background-position-y: -29px;
}
.button-row button {
  height: 50px;
  width: 50px;
  border: none;
  background: url("~@/assets/img/switch.png");
  background-size: contain;
  background-position: center center;
  cursor: pointer;
  margin: 0 auto;
  //   border: 4px solid lightgrey;
  //   background-color: #ede9e1;
  outline: none;
}

.led-row {
  text-align: center;
  margin: 8px 0;
}
</style>
