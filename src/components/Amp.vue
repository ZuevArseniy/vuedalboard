<template>
  <div>
    <Header :input="pedalboardIn" @source="onSetSource" />
    <Pedalboard :input="pedalboardIn" :output="pedalboardOut" />
  </div>
</template>

<script>
import Header from "./Header";
import Pedalboard from "./Pedalboard";
import ctx from "../web-audio/audio-context.js";

export default {
  name: "Amp",
  components: {
    Header,
    Pedalboard,
  },
  data() {
    const pedalboardOut = ctx.createGain();
    pedalboardOut.connect(ctx.destination);
    return {
      source: null,
      pedalboardIn: ctx.createGain(),
      pedalboardOut,
    };
  },
  methods: {
    onSetSource(source) {
      if (this.source) {
        this.source.disconnect(this.pedalboardIn);
      }
      source.connect(this.pedalboardIn);
      this.source = source;
    },
  },
};
</script>

<style lang="scss">
.container {
  min-height: calc(100vh - 100px);
  display: flex;
  flex-wrap: wrap;
}
.compressor {
  background-color: #e34c2f;
}
</style>
