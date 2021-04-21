<template>
    <div class="pedal compressor">
        <div class="controls">
            <Knob/>
            <Knob/>
        </div>
        <div class="title-top">PJAMP</div>
        <div class="led-row">
            <span class="led"></span>
        </div>
        <div class="button-row">
            <button></button>
        </div>
        <div class="title-bottom">
            Atsuryoku Compressor
        </div>
    </div>
</template>

<script>
    import Knob from "../elements/Knob";
    export default {
        name: "Delay",
        components: {Knob}
    }
</script>

<style scoped>

</style>
