<template>
  <div class="wrapper">
    <Tinder
    ref="tinder"
    :queue="queue"
    @submit="submit" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Tinder from '@/components/Tinder.vue';

@Component({
  components: {
    Tinder,
  },
})
export default class Home extends Vue {
  public queue: any[] = [];

  public created() {
    this.getData();
  }

  public getData() {
    const list = [
      {
        key: 1,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/2c1e81d103c99244213d2481d27b63fecf23b945.jpg',
        name: 'Ryo',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/4bd4f7f73f98c43a9c6cd20db67d4b36250d2bf5.jpg',
      },
      {
        key: 2,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/9203bf7ba304a03d16f516300cc34284ffafa2a9.jpg',
        name: 'RHplus',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/34c807a43918f04ffb376d63d1d6254ee5e49b5a.jpg',
      },
      {
        key: 3,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/99f3604c956f9b58639eba6d4b1cd7bcc858d372.jpg',
        name: 'ruchico',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/a704f8640f55228c3ed787f0e1b3fc551c3892e3.jpg',
      },
    ];

    this.queue = this.queue.concat(list);
  }

  public submit(choice: string) {
    switch (choice) {
      case 'nope': // 左滑
        break;
      case 'like': // 右滑
        break;
      case 'super': // 上滑
        break;
    }
    if (this.queue.length < 2) {
      this.getData();
    }
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  height: calc(100vh - 50px);
  display: flex;
  align-items: center;
}
</style>
