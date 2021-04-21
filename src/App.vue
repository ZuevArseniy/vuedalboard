<template>
  <Amp />
</template>

<script>
import Amp from "./components/Amp.vue";

export default {
  name: "App",
  components: {
    Amp,
  },
};
</script>

<style>
@font-face {
  font-family: "Moyko";
  src: url("~@/assets/fonts/Moyko.ttf") format("truetype");
}

*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}
body {
  background-color: #ede9e1;
  font-family: Moyko;
  letter-spacing: 3px;
}
</style>