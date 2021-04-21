<template>
  <div class="knob" ref="knob">
    <div class="rotary" @mousedown="engage" ref="rotary"></div>
    <label>{{ label }}</label>
  </div>
</template>

<script>
const MIN_DEGREE = -150;
const MAX_DEGREE = 150;
export default {
  name: "Knob",
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
    defaultValue: {
      type: Number,
    },
  },
  computed: {  
    value() {
      return this.min + Math.abs(this.max - this.min) * this.rotatePercent;
    },
    valueRange() {
      return Math.abs(this.max - this.min);
    },
    rotatePercent() {
      return Math.abs(this.rotatedDeg - this.minDeg) / this.degRange;
    },
    degRange() {
      return Math.abs(this.maxDeg - this.minDeg);
    },
  },
  data() {
    return {
      minDeg: MIN_DEGREE,
      maxDeg: MAX_DEGREE,
      rotatedDeg: 0,
      diff: 0,
      prevY: null,
      engaged: false,
    };
  },
  methods: {
    engage(event) {
      this.engaged = true;
      this.prevY = event.clientY;
      event.preventDefault();
    },
    disengage() {
      if (this.engaged) {
        this.rotatedDeg = this.diff;
        this.diff = 0;
        this.$emit("change", this.value);
        this.engaged = false;
      }
    },
    rotaryMove(Y) {
      if (this.engaged) {
        if (this.prevY - Y === 0) {
          return;
        }
        const goingUp = this.prevY >= Y;
        let diff = Math.abs(this.prevY - Y); //1px per 1degree rotate
        if (goingUp) {
          diff = diff + this.rotatedDeg;
          if (diff > this.maxDeg) {
            diff = this.maxDeg;
          }
          this.setRotation(diff);
        } else {
          diff = this.rotatedDeg - diff;
          if (diff < this.minDeg) {
            diff = this.minDeg;
          }
          this.setRotation(diff);
        }
        this.diff = diff;
        this.$emit("change", this.value);
      }
    },
    rotateDegByValue(value) {
      return (value / this.valueRange) * this.degRange + this.minDeg;
    },
    setRotation(degree) {
        this.$refs.rotary.style = "transform: rotate(" + degree + "deg)";
    }
  },
  watch: {
    defaultValue: function (newVal) {
      this.rotatedDeg = this.rotateDegByValue(newVal);
      this.setRotation(this.rotatedDeg);
      this.$emit("change", this.value);
    },
  },
  mounted() {
    let self = this;
    window.addEventListener("mouseup", this.disengage);
    window.addEventListener("mousemove", event => self.rotaryMove(event.clientY));
    if (this.defaultValue) {
      this.rotatedDeg = this.rotateDegByValue(this.defaultValue);
      this.setRotation(this.rotatedDeg);
    }
    this.$emit("change", this.value);
  },
};
</script>

<style lang="scss" scoped>
.knob {
  text-align: center;
  label {
    display: inline-block;
    margin: 5px 0 10px 0;
  }
}
</style>
