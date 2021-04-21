<template>
  <StompBase
    label="Origami Cabinet"
    class="amp"
    :input-outer="input"
    :output-outer="output"
    :input-inner="inNode"
    :output-inner="outNode"
  >
    <PolyKnob
      id="knob-1"
      :min="0"
      :max="7"
      label="volume"
      @change="setVolume"
      :default-value="4"
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
      inNode: ctx.createConvolver(),
      outNode: ctx.createGain(),
      impulse: null,
      volume: 2,
    };
  },
  async created() {
    let response = await fetch("audio/impulse.wav");
    let buffer = await response.arrayBuffer();
    this.impulse = await ctx.decodeAudioData(buffer);
    this.build();
  },
  methods: {
    build() {
      this.outNode.gain.setValueAtTime(this.volume, ctx.currentTime);
      this.inNode.buffer = this.impulse;
      this.inNode.connect(this.outNode);
    },
    setVolume(volume) {
      this.volume = volume;
      this.outNode.gain.setValueAtTime(volume, ctx.currentTime);
    },
  },
};
</script>

<style scoped>
.amp {
  background-color: #5d7e4e;
  width: 120px;
}
</style>