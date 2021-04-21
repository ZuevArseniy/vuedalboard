<template>
  <div class="pedal">
    <div class="controls">
      <slot></slot>
    </div>
    <div class="title-top">PJAMP</div>
    <Toggle :turned-on="turnedOn" @toggle="toggle" />
    <div class="title-bottom">{{label}}</div>
  </div>
</template>

<script>
import ctx from "../../web-audio/audio-context.js";
import Toggle from "../elements/Toggle";

export default {
  components: {
    Toggle,
  },
  props: {
    inputOuter: {
      type: AudioNode,
      required: true,
    },
    outputOuter: {
      type: AudioNode,
      required: true,
    },
    inputInner: {
      type: AudioNode,
      required: true,
    },
    outputInner: {
      type: AudioNode,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
  },
  data() {
    let bypass = ctx.createGain();
    bypass.gain.setValueAtTime(1, ctx.currentTime);
    let effect = ctx.createGain();
    effect.gain.setValueAtTime(0, ctx.currentTime);
    return {
      turnedOn: false,
      bypass,
      effect,
    };
  },
  created() {
    // create bypass route
    this.inputOuter.connect(this.bypass);
    this.bypass.connect(this.outputOuter);

    // create effect route
    this.inputOuter.connect(this.effect);
    this.effect.connect(this.inputInner);
    this.outputInner.connect(this.outputOuter);
  },
  methods: {
    toggle() {
      this.turnedOn = !this.turnedOn;
      this.effect.gain.setValueAtTime(Number(this.turnedOn), ctx.currentTime);
      this.bypass.gain.setValueAtTime(Number(!this.turnedOn), ctx.currentTime);
    },
  },
};
</script>

<style lang="scss" scoped>

  .pedal {
    width: 186px;
    height: 320px;
    padding: 15px;
    border-radius: 10px;
    margin: 30px;
    display: flex;
    color: white;
    flex-direction: column;
    position: relative;
    box-sizing: content-box;
  }

  .pedal::after {
    background-color: #9d9c9c;
    position: absolute;
    left: -10px;
    top: 141px;
    height: 40px;
    width: 10px;
    content: '';
    z-index: -9;
  }

  .pedal::before {
    background-color: #9d9c9c;
    position: absolute;
    right: -10px;
    top: 141px;
    height: 40px;
    width: 10px;
    content: '';
    z-index: -9;
  }

  .controls {
    height: 130px;
    display: flex;
    justify-content: center;
  }
  .title-top {
    font-size: 30px;
    text-align: center;
  }
  .title-bottom {
    text-align: center;
    font-size: 25px;
    margin-top: 15px;
  }
</style>