<template>
  <div>
    <div class="led-row">
      <span class="led" :class="{ on: turnedOn }"></span>
    </div>
    <div class="button-row">
      <button @click="toggle"></button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Toggle",
  props: ["turnedOn"],
  methods: {
    toggle() {
      this.$emit("toggle");
    },
  },
};
</script>

<style lang="scss" scoped>
.led {
  &.on {
    background-color: red;
  }
}
.button-row {
  text-align: center;
}

.led {
  display: inline-block;
  height: 15px;
  border-radius: 10px;
  width: 15px;
  background-color: #ea7c7c;
  border: 2px solid black;
}
.led.on {
  background-color: red;
}
.button-row button {
  height: 40px;
  width: 40px;
  border-radius: 20px;
  cursor: pointer;
  margin: 0 auto;
  border: 4px solid lightgrey;
  background-color: #ede9e1;
  outline: none;
}

.led-row {
  text-align: center;
  margin: 8px 0;
}
</style>
