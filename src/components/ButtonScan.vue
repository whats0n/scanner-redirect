<template>
  <button
    class="btn-scan"
    @click.prevent="$emit('click', $event)"
  >
    <span class="btn-scan__text">SCAN</span>
  </button>
</template>

<script>
export default {
  name: 'ButtonScan'
}
</script>

<style lang="scss">
.btn-scan {
  display: inline-block;
  vertical-align: top;
  border-radius: 25px;
  width: 100px;
  height: 50px;
  color: #fff;
  background: #333;
  font-size: 20px;
  font-weight: 700;
  line-height: 50px;
  text-transform: uppercase;
  cursor: pointer;
}
.btn-scan__text {
  display: inline-block;
  vertical-align: top;
}
</style>
