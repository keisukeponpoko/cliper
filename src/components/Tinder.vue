<template>
  <div style="width: 100%;">
    <div class="vue-tinder-wrapper">
      <div
        class="vue-tinder"
        @touchstart="start"
        @touchmove="move"
        @touchend="end"
        @touchcancel="end"
        @mousedown="start"
        @mousemove="move"
        @mouseup="end"
        @mouseout="end">
        <transition-group
          @leave="leave"
          @afterLeave="gone">
          <TinderCard
            v-if="index<2"
            :style="isCur(index)?mainCardStyle():benchCardStyle(index)"
            v-for="(item,index) in queue"
            :key="item[keyName]">
            <div
              class="pic"
              :style="`background-image:url(${item.url})`">
            </div>
            <div class="user_info">
              <img class="user_icon" :src="item.icon" />
              <p class="user_name">{{ item.name }}</p>
            </div>

            <template v-if="isCur(index)">
              <span slot="nope" class="pointer-wrap nope-pointer-wrap" :style="{opacity:nopeOpacity}">
                <img class="nope-pointer" :opacity="nopeOpacity" src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/nope-txt.png">
              </span>
              <span slot="like" class="pointer-wrap like-pointer-wrap" :style="{opacity:likeOpacity}">
                <img class="like-pointer" :opacity="likeOpacity" src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/like-txt.png">
              </span>
              <span v-if="allowSuper" slot="super" class="pointer-wrap super-pointer-wrap" :style="{opacity:superOpacity}">
                <img class="super-pointer" :opacity="superOpacity" src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/super-txt.png">
              </span>
            </template>
          </TinderCard>
        </transition-group>
      </div>
    </div>
    <div class="btns">
      <img src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/nope.png" @click="decide('nope')">
      <img src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/super-like.png" @click="decide('super')">
      <img src="https://johnnydan.oss-cn-beijing.aliyuncs.com/vue-tinder/like.png" @click="decide('like')">
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';
import TinderCard from '@/components/TinderCard.vue';

let resizeTimer: any;

@Component({
  components: {
    TinderCard,
  },
})
export default class Home extends Vue {
  /** props */
  @Prop() public queue!: any;

  public status: number = 0;
  public size: any = {
    top: 0,
    width: 0,
    height: 0,
  };
  public state: any = {
    touchId: null,
    start: {},
    move: {},
    startPoint: 1, // 1：上，-1：下
    ratio: 0,
    result: null,
  };
  public allowSuper: boolean = true;
  public keyName: string = 'key';
  public pointerThreshold: number = 0.5;
  public superThreshold: number = 0.5;

  get nowKey(): string | null {
    if (this.status === 2) {
      return null;
    }
    return this.queue[0] && this.queue[0][this.keyName];
  }

  get pointerOpacity(): number {
    return this.state.ratio / this.pointerThreshold;
  }

  get likeOpacity(): number {
    if (this.superOpacity) {
      return 0;
    }
    return this.pointerOpacity;
  }

  get nopeOpacity(): number {
    return -this.likeOpacity;
  }

  get superOpacity(): number {
    if (!this.allowSuper) {
      return 0;
    }
    const disY = this.state.move.y - this.state.start.y;
    const ratio = disY / (-this.superThreshold * this.size.height);
    const pointerOpacity = Math.abs(this.pointerOpacity);
    return ratio > pointerOpacity ? ratio : 0;
  }

  public mounted(): void {
    this.size = {
      top: this.$el.offsetTop,
      width: this.$el.offsetWidth,
      height: this.$el.offsetHeight,
    };
    window.onresize = this.getSize;
  }

  public destroyed(): void {
    window.removeEventListener('onresize', this.getSize);
  }

  public getSize(): void {
    clearInterval(resizeTimer);
    resizeTimer = setTimeout(() => {
      this.size = {
        top: this.$el.offsetTop,
        width: this.$el.offsetWidth,
        height: this.$el.offsetHeight,
      };
    }, 300);
  }

  public isCur(index: number): boolean {
    return index === 0 && this.queue[index][this.keyName] === this.nowKey;
  }

  public start(e: any): void {
    const state = this.state;
    if (state.touchId !== null || this.status === 2) {
      return;
    }
    let pageX;
    let pageY;
    if (e.type === 'touchstart') {
      pageX = e.changedTouches[0].pageX;
      pageY = e.changedTouches[0].pageY;
      // TODO: iOS侧滑返回区域，不应该继续，这个区域还需要调整，有必要的话还要区分下iOS/Android
      // if (pageX < ?) {
      //   return
      // }
    } else {
      pageX = e.clientX;
      pageY = e.clientY;
    }
    // 判断触摸起始位置在卡片的上部还是下部
    const top = this.size.top;
    const height = this.size.height;
    const centerY = (top + height / 2);
    const startPoint = pageY > centerY ? -1 : 1;
    // 初始化
    this.status = 1;
    this.state = {
      touchId: e.type === 'touchstart' ? e.changedTouches[0].identifier : 'mouse',
      start: {
        x: pageX,
        y: pageY,
      },
      move: Object.create(null),
      startPoint,
      ratio: 0,
      result: null,
    };
  }

  public move(e: any): void {
    e.preventDefault();
    const state = this.state;
    if (state.touchId === null ||
        this.status === 2 ||
        (e.type === 'touchmove' && state.touchId !== e.changedTouches[0].identifier)) {
      return;
    }
    let pageX;
    let pageY;
    if (e.type === 'touchmove') {
      pageX = e.changedTouches[0].pageX;
      pageY = e.changedTouches[0].pageY;
    } else {
      pageX = e.clientX;
      pageY = e.clientY;
    }
    state.move = {
      x: pageX,
      y: pageY,
    };
  }

  public end(e: any): void {
    if (e.type === 'touchstart' && this.state.touchId !== e.changedTouches[0].identifier) {
      return;
    }
    if (this.status === 2) {
      return;
    }
    if (Math.abs(this.pointerOpacity) >= 1 || this.superOpacity >= 1) {
      const result = this.superOpacity >= 1 ? 'super' : this.pointerOpacity > 0 ? 'like' : 'nope';
      this.shiftCard(result);
    } else {
      this.gone();
    }
  }

  public shiftCard(type: string): void {
    this.status = 2;
    this.state.result = type;
    const item = this.queue.shift();
    this.$emit('update:queue', this.queue);
    this.submitDecide(type, item[this.keyName]);
  }

  public mainCardStyle(): object {
    const style = { zIndex: 10, transform: '', transition: '' };
    if (this.status === 0) { // 初始化位置
      style.transform = 'scale(1) translate3d(0,0,0) rotate(0deg)';
      style.transition = 'all 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275)';
    } else if (this.status === 1) { // 移动中，位移及旋转角度
      const state = this.state;
      const {start, move, startPoint} = state;
      const x = move.x - start.x || 0;
      const y = move.y - start.y || 0;
      // 横向滑动卡片一半宽(0.5)时为标准状态
      const ratio = x / (this.size.width * 0.5);
      state.ratio = ratio;
      // 标准状态（ratio为1时）10度角
      const rotate = 10 * ratio * startPoint;
      style.transform = `translate3d(${x}px,${y}px,0) rotate(${rotate}deg)`;
      style.transition = 'none';
    }
    return style;
  }

  public benchCardStyle(index: number): object {
    // 当前条件卡片不应该被显示
    if ((index === 1 && this.status === 2) || index > 1) {
      return {
        zIndex: -1,
        opacity: 0,
      };
    }
    const style = { zIndex: 9, transform: '', transition: '' };

    if (this.status === 0) { // 初始化替补图位置
      style.transform = 'scale3d(0.95,0.95,1)';
      style.transition = 'all 500ms ease';
    } else if (this.status === 1) { // 移动中，替补图渐渐放大
      // scale区间：[0.95-1]
      let ratio = this.state.ratio;
      if (ratio > 1) {
        ratio = 1;
      } else if (ratio < -1) {
        ratio = -1;
      }
      style.transform = `scale3d(${Math.abs(ratio) * 0.05 + 0.95},${Math.abs(ratio) * 0.05 + 0.95},1)`;
      style.transition = 'none';
    } else if (this.status === 2) { // 第一张在消失中，第二张替补为主图，要过渡到最大
      style.transform = 'scale3d(1,1,1)';
      style.transition = 'all 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275)';
    }
    return style;
  }

  public leave(el: any, done: any): void {
    const state = this.state;
    const {start, move, startPoint} = state;
    let x = move.x - start.x || 0;
    let y = move.y - start.y || 0;
    if (state.result === 'super') {
      y -= this.size.width;
    } else {
      x += this.size.width * (x < 0 ? -0.5 : 0.5);
      y *= x / (move.x - start.x);
    }
    const ratio = x / (this.size.width * 0.5);
    const rotate = (ratio / (0.8 / 0.5)) * 15 * startPoint;
    const duration = state.touchId === null ||
                     state.result === 'super' ? 800 : 300;
    el.className += ` ${state.result}`;
    el.style.opacity = 0;
    el.style.transform = `translate3d(${x}px,${y}px,0) rotate(${rotate}deg)`;
    el.style.transition = `all ${duration}ms ease`;
    setTimeout(done, duration);
  }

  public gone(): void {
    this.status = 0;
    this.state = {
      touchId: null,
      start: {},
      move: {},
      startPoint: 1, // 1：上，-1：下
      ratio: 0,
      result: null,
    };
  }

  public decide(type: string): void {
    if (this.state.touchId || this.status !== 0 || !this.nowKey) {
      return;
    }
    this.state.start = {x: 0, y: 0};
    this.state.move = {
      x: type === 'super' ? 0 : type === 'like' ? 1 : -1,
      y: type === 'super' ? -1 : 0,
    };
    this.state.startPoint = 1;
    this.shiftCard(type);
  }

  public submitDecide(type: string, key: string): void {
    this.$emit('submit', {type, key});
  }
}
</script>

<style lang="scss" scoped>
.vue-tinder-wrapper {
  height: 420px;
  position: relative;
}

.vue-tinder {
  position: absolute;
  z-index: 1;
  left: 0;
  right: 0;
  top: 0;
  margin: auto;
  width: calc(100% - 20px);
  height: 100%;
  min-width: 300px;
  max-width: 355px;
}

/* style正在被数据绑定，只能使用important来覆盖 */
.v-move { transition: none!important; }
.pointer-wrap {
  pointer-events: none;
  transition: opacity .2s ease;
}
/* 通过调用函数让卡片消失时需要直接显示对应指示器，不需要渐变 */
.tinder-card.nope .nope-pointer-wrap,
.tinder-card.like .like-pointer-wrap,
.tinder-card.super .super-pointer-wrap {
  opacity: 1!important;
}

/* 按钮样式 */
.btns {
  margin: auto;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 300px;
  max-width: 355px;
}
.btns img{ width: 80px; }

/* 卡片内的3种状态指示器位置，透明度会由组件自动调整 */
.nope-pointer { right: 10px; }
.like-pointer { left: 10px; }
.nope-pointer,
.like-pointer {
  position: absolute;
  z-index: 1;
  top: 20px;
  width: 64px;
  height: 64px;
}
.super-pointer {
  position: absolute;
  z-index: 1;
  bottom: 80px;
  left: 0;
  right: 0;
  margin: auto;
  width: 112px;
  height: 78px;
}

/* slot内图片样式 */
.pic {
  width: 350px;
  height: 350px;
  background-size: cover;
}

.user_info {
  margin-left: 20px;
  display: flex;
  align-items: center;
}

.user_icon {
  width: 40px;
  height: 40px;
  border-radius: 20px;
}

.user_name {
  font-size: 16px;
  margin-left: 8px;
}
</style>
