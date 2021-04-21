<template>
  <div class="container">
    <Overdrive :input="input" :output="OdTrWire" />
    <Tremolo :input="OdTrWire" :output="TrAwWire" />
    <Autowah :input="TrAwWire" :output="AwRvWire" />
    <Reverb :input="AwRvWire" :output="RvCabWire" />
    <Cabinet :input="RvCabWire" :output="output" />
  </div>
</template>

<script>
import Cabinet from "./stomp/Cabinet";
import Overdrive from "./stomp/Overdrive";
import Reverb from "./stomp/Reverb";
import Tremolo from "./stomp/Tremolo";
import Autowah from "./stomp/Autowah";
import io from "../utils/io";
import ctx from "../web-audio/audio-context.js";

export default {
  props: { ...io },
  components: {
    Tremolo,
    Reverb,
    Overdrive,
    Cabinet,
    Autowah,
  },
  data() {
    return {
      OdTrWire: ctx.createGain(),
      TrAwWire: ctx.createGain(),
      AwRvWire: ctx.createGain(),
      RvCabWire: ctx.createGain(),
    };
  },
};
</script>

<style scoped>
</style>
