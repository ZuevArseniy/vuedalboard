<template>
  <StompBase
    label="Ahiru Autowah"
    class="autowah"
    :input-outer="input"
    :output-outer="output"
    :input-inner="inNode"
    :output-inner="outNode"
  >
    <PolyKnob
      :min="10"
      :max="80"
      label="speed"
      @change="setSpeed"
      :default-value="20"
    />
    <PolyKnob
      :min="0"
      :max="1"
      label="depth"
      @change="setMix"
      :default-value="0.7"
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
      mix: 0.7,
      minFreq: 350,
      maxFreq: 2200,
      down: true,
      speed: 20,
      wetNode: ctx.createGain(),
      filter: ctx.createBiquadFilter(),
      filterGain: ctx.createGain(),

      oscilatorWaveShaper: null,
      oscillator: null,
      oscillatorGain: null,
    };
  },
  created() {
    this.build();
  },
  methods: {
    setMix(value) {
      this.mix = value;
      this.wetNode.gain.value = value;
      this.dryNode.gain.value = 1 - value;
    },
    setSpeed(value) {
      this.speed = value;
      this.oscillator.frequency.value = value;
    },
    build() {
      this.filterGain.gain.value = 2;
      this.wetNode.gain.value = this.mix;
      this.dryNode.gain.value = 1 - this.mix;

      this.filter.frequency.value = 1150;
      this.filter.type = "bandpass";
      this.filter.q = 800;
      setInterval(this.moveFreq, 3);
      this.inNode.connect(this.dryNode).connect(this.outNode);
      this.inNode
        .connect(this.filter)
        .connect(this.filterGain)
        .connect(this.wetNode)
        .connect(this.outNode);
    },
    moveFreq() {
      let currentFreq = this.filter.frequency.value;
      if (this.down) {
        if (currentFreq <= this.minFreq) {
          this.down = !this.down;
          this.filter.frequency.value = currentFreq + this.speed;
        } else {
          this.filter.frequency.value = currentFreq - this.speed;
        }
      } else {
        if (currentFreq >= this.maxFreq) {
          this.down = !this.down;
          this.filter.frequency.value = currentFreq - this.speed;
        } else {
          this.filter.frequency.value = currentFreq + this.speed;
        }
      }
    },
  },
};
</script>

<style scoped>
.autowah {
  background-color: #53657e;
}
</style>
