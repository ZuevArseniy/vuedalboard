<template>
  <StompBase
    label="Sacura Overdrive"
    class="overdrive"
    :input-outer="input"
    :output-outer="output"
    :input-inner="overdriveInNode"
    :output-inner="overdriveOutNode"
  >
    <PolyKnob
      :min="0"
      :max="400"
      label="effect"
      @change="setEffect"
      :default-value="150"
    />
    <PolyKnob
      :min="0"
      :max="1"
      label="volume"
      @change="setVolume"
      :default-value="0.5"
    />
  </StompBase>
</template>

<script>
import ctx from "../../web-audio/audio-context.js";
import io from "../../utils/io"
import StompBase from "./StompBase";
import PolyKnob from "../elements/PolyKnob";

export default {
  props: { ...io },
  components: { StompBase, PolyKnob },
  data() {
    return {
      overdriveInNode: ctx.createWaveShaper(),
      overdriveOutNode: ctx.createGain(),
      effect: 150,
      volume: 0.5,
    };
  },
  created() {
    this.build();
  },
  methods: {
    setEffect(effect) {
      this.effect = effect;
      if (this.overdriveInNode) {
        this.overdriveInNode.curve = this.makeDistortionCurve(effect);
      }
    },
    setVolume(volume) {
      this.volume = volume;
      if (this.overdriveOutNode) {
        this.overdriveOutNode.gain.setValueAtTime(volume, ctx.currentTime);
      }
    },
    build() {
      this.overdriveInNode.curve = this.makeDistortionCurve(this.effect);
      this.overdriveOutNode.gain.setValueAtTime(this.volume, ctx.currentTime);
      this.overdriveInNode.connect(this.overdriveOutNode);
      console.log(this.overdriveOutNode)
    },
    makeDistortionCurve(amount) {
      let k = typeof amount === "number" ? amount : 50,
        n_samples = 44100,
        curve = new Float32Array(n_samples),
        deg = Math.PI / 180,
        i = 0,
        x;
      for (; i < n_samples; ++i) {
        x = (i * 2) / n_samples - 1;
        curve[i] = ((3 + k) * x * 20 * deg) / (Math.PI + k * Math.abs(x));
      }
      return curve;
    },
  },
};
</script>

<style scoped>
.overdrive {
  background-color: #562237;
}
</style>
