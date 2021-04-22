<template>
  <header>
    <div class="left-block">
      <img class="logo" src="~@/assets/img/input.png" />
      <label for="">input</label>
    </div>
    <div class="dd-column">
      <label>Source</label>
      <select v-on:change="selectSource($event)">
        <option value="samples">samples</option>
        <option value="mic">audio input</option>
      </select>
    </div>
    <div class="toggle" :class="{ on: turnedOn }" @click="toggle"></div>
  </header>
</template>

<script>
import ctx from "../web-audio/audio-context.js";

export default {
  props: {
    input: {
      type: AudioNode,
      requied: true,
    },
  },
  data() {
    return {
      turnedOn: false,
      sampleBuffer: null,
      source: null,
      inputStream: null,
      inputSource: null,
      sampleSource: null,
    };
  },
  created() {
    fetch("audio/demo.mp3")
      .then((response) => response.arrayBuffer())
      .then((arrayBuffer) => ctx.decodeAudioData(arrayBuffer))
      .then((audioBuffer) => {
        this.sampleBuffer = audioBuffer;
      })
      .then(this.selectSource);
  },
  methods: {
    toggle() {
      ctx.resume();
      this.turnedOn = !this.turnedOn;
      this.input.gain.setValueAtTime(Number(this.turnedOn), ctx.currentTime)
    },
    async selectSource(event) {
      if (event && event.target.value === "mic") {
        if (!this.inputStream) {
          this.inputStream = await navigator.mediaDevices.getUserMedia({
            audio: {
              echoCancellation: false,
            },
            video: false,
          });

          this.inputSource = ctx.createMediaStreamSource(this.inputStream);
        }
        this.sampleSource.disconnect(this.input)
        this.inputStream.connect(this.input)
      } else {
        if (!this.sampleSource) {
          this.sampleSource = ctx.createBufferSource();
          this.sampleSource.buffer = this.sampleBuffer;
          this.sampleSource.loop = true;
          this.sampleSource.start();
        }
        if (this.inputStream) {
            this.inputStream.disconnect(this.input)
        }
        this.sampleSource.connect(this.input)
      }
      
    },
  },
};
</script>

<style lang="scss" scoped>
header {
  background-image: url("~@/assets/img/golden.jpg");
  background-size: 100%;
  padding: 20px 50px;
  height: 100px;
  display: flex;
  align-items: center;
  box-sizing: content-box;
  border: 15px solid black;
  border-radius: 0 0 10px 10px;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.75);

  .left-block {
    display: flex;
    align-items: center;
    flex-direction: column;
    .logo {
      height: 50px;
      width: 50px;
      margin-bottom: 5px;
    }
    .title {
      font-size: 50px;
      margin-left: 30px;
    }
  }

  .dd-column {
    margin-left: 60px;
    label {
      display: block;
      font-size: 30px;
    }
    select {
      font-size: 30px;
      height: 40px;
    }
  }

  .toggle {
    background-image: url("~@/assets/img/toggle-off.png");
    height: 100px;
    width: 52px;
    cursor: pointer;
    background-size: 52px;
    border-radius: 4px;
    margin-left: auto;

    &.on {
      background-image: url("~@/assets/img/toggle-on.png");
    }
  }
}
</style>
