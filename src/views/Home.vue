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
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/c3ed78860ce1479cc8b6f11c682f756ad8554fc8.jpg',
        name: 'Ryo',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/4bd4f7f73f98c43a9c6cd20db67d4b36250d2bf5.jpg',
      },
      {
        key: 2,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/02cf8865dcdbf0b1218ae43f559e2c652cbd690e.jpg',
        name: 'RHplus',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/34c807a43918f04ffb376d63d1d6254ee5e49b5a.jpg',
      },
      {
        key: 3,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/2412e7ac76fd5cbc1082fc189cca75e504151d8d.jpg',
        name: 'ruchico',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/a704f8640f55228c3ed787f0e1b3fc551c3892e3.jpg',
      },
      {
        key: 4,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/fd8d8f2b907a4b3427fabfad3a6315053ec9206b.jpg',
        name: 'RABI',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/27e20536b927760c2fd13d76f71ae0066266f826.jpg',
      },
      {
        key: 5,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/988d88e01bb0a957ad804db4f23f331cb45d3222.jpg',
        name: 'ras',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/0b90b45ef2fa3c538b97eb8f25b8f6be7d1533be.jpg',
      },
      {
        key: 6,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/951d24c7adff3eba361f4fa6c055dedea42e5832.jpg',
        name: 'rash',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/5e731ee98762b62bd50f6afd6df7c3fac332d8bc.jpg',
      },
      {
        key: 7,
        url: 'https://cdn.roomclip.jp/v1/320/roomclip-bucket/img_320/ce5188441957d6340fb9bf46e14f10a2b6885d6b.jpg',
        name: 'greenrose',
        icon: 'https://cdn.roomclip.jp/v1/64/roomclip-bucket/img_user/ae9507b1228ea35d4229a793aee894cd140b8e4d.jpg',
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
