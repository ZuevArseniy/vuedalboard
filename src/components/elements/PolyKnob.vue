<script>
import "../../lib/webaudio-controls";

export default {
  props: {
    min: {
      type: Number,
      default: 0,
    },
    max: {
      type: Number,
      default: 100,
    },
    label: {
      type: String,
      default: "label",
    },
    type: {
        type: String,
        default: 'boss'
    },
    width: {
        type: Number,
        default: 75
    },
    height: {
        type: Number,
        default: 75
    },
    defaultValue: {
      type: Number,
    },
  },
  computed: {
    sprite() {
      return require(`@/assets/img/${this.type}.png`);
    },
  },
  methods: {
    onInput(event) {
      this.$emit("change", event.target.value);
    },
  },
};
</script>

<template>
  <div class="knob">
    <webaudio-knob
      :src="sprite"
      :min="min"
      :max="max"
      :step="0.1"
      :width="width"
      :value="defaultValue"
      :height="height"
      @input="onInput"
    ></webaudio-knob>

    <label for="">{{ label }}</label>
  </div>
</template>

<style lang="scss" scoped>
.knob {
  background: none;
  text-align: center;
  width: 75px;
  margin: 0 10px;
  label {
    display: inline-block;
    margin: 5px 0 10px 0;
  }
}
</style>