<template>
  <StompBase
    label="Chashitsu Reverb"
    class="reverb"
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
      :default-value="0.3"
    />
  </StompBase>
</template>

<script>
import ctx from "../../web-audio/audio-context.js";
import io from "../../utils/io"
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
      convNode: ctx.createConvolver(),
      outNode: ctx.createGain(),
      dryNode: ctx.createGain(),
      convVolume: ctx.createGain(),
      mix: 0.3,
      wetNode: ctx.createGain(),
      impulse: null,
    };
  },
  async created() {
    let response = await fetch("audio/greek_rev.wav");
    let buffer = await response.arrayBuffer();
    this.impulse = await ctx.decodeAudioData(buffer);
    this.build();
  },
  methods: {
    build() {
      this.convVolume.gain.setValueAtTime(3, ctx.currentTime);
      this.wetNode.gain.setValueAtTime(this.mix, ctx.currentTime);
      this.dryNode.gain.setValueAtTime(1 - this.mix, ctx.currentTime);
      this.convNode.buffer = this.impulse;
      this.inNode
        .connect(this.dryNode)
        .connect(this.outNode);
      this.inNode
        .connect(this.convNode)
        .connect(this.convVolume)
        .connect(this.wetNode)
        .connect(this.outNode);
    },
    setVolume(volume) {
      this.volume = volume;
      if (this.outNode) {
        this.outNode.gain.setValueAtTime(volume, ctx.currentTime);
      }
    },
  },
};
</script>

<style scoped>
.reverb {
  background-color: #d98f0d;
  width: 120px;
}
</style>