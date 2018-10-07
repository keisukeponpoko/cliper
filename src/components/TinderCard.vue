<template>
  <div class="tinder-card">
    <slot></slot>
    <slot name="nope"></slot>
    <slot name="like"></slot>
    <slot name="super"></slot>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component({})
export default class Home extends Vue {}
</script>

<style lang="scss" scoped>
.tinder-card{
  position: absolute;
  width: 100%;
  height: auto;
  overflow: hidden;
  background: #fefefe;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0,0,0,.15);
}
</style>
