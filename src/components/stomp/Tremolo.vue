<template>
  <StompBase
    label="Myakudo Tremolo"
    class="tremolo"
    :input-outer="input"
    :output-outer="output"
    :input-inner="inNode"
    :output-inner="outNode"
  >
    <PolyKnob
      :min="0"
      :max="1"
      label="mix"
      @change="setMix"
      :default-value="0.5"
    />
    <PolyKnob
      :min="0"
      :max="10"
      label="speed"
      @change="setSpeed"
      :default-value="3"
    />
  </StompBase>
</template>

<script>
import ctx from "../../web-audio/audio-context.js";
import io from "../../utils/io";
import StompBase from "./StompBase";
import PolyKnob from "../elements/PolyKnob";

export default {
  components: {
    StompBase,
    PolyKnob,
  },
  props: { ...io },
  data() {
    return {
      inNode: ctx.createGain(),
      outNode: ctx.createGain(),
      dryNode: ctx.createGain(),
      tremoloGain: ctx.createGain(),
      mix: 0.5,
      speed: 3,
      wetNode: ctx.createGain(),
      oscillator: ctx.createOscillator(),
    };
  },
  created() {
    this.build();
  },
  methods: {
    setMix(value) {
      this.mix = value;
      this.wetNode.gain.setValueAtTime(value, ctx.currentTime);
      this.dryNode.gain.setValueAtTime(1 - value, ctx.currentTime);
    },
    setSpeed(value) {
      this.speed = value;
      this.oscillator.frequency.setValueAtTime(value, ctx.currentTime);
    },
    build() {
      this.oscillator.frequency.setValueAtTime(this.speed, ctx.currentTime);
      this.oscillator.connect(this.tremoloGain.gain);
      this.oscillator.start();
      this.wetNode.gain.setValueAtTime(this.mix, ctx.currentTime);
      this.dryNode.gain.setValueAtTime(1 - this.mix, ctx.currentTime);
      this.inNode.connect(this.dryNode).connect(this.outNode);
      this.inNode
        .connect(this.tremoloGain)
        .connect(this.wetNode)
        .connect(this.outNode);
    },
  },
};
</script>

<style scoped>
.tremolo {
  background-color: #c6233e;
}
</style>
