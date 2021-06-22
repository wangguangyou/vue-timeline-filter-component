<template>
  <div role="slider" class="vtf-slider">
    <div @click="onSliderClick" ref="slider" class="vtf-slider__runway">
      <div class="vtf-slider__bar" :style="barStyle"></div>
      <div tabindex="0" class="vtf-slider__button-wrapper" style="left: 10%;">
        <div :style="{ left: this.leftPosition }" class="vtf-tooltip vtf-slider__button" tabindex="0"></div>
      </div>
      <div tabindex="0" class="vtf-slider__button-wrapper" style="left: 90%;">
        <div :style="{ left: this.rightPosition }" class="vtf-tooltip vtf-slider__button" tabindex="0"></div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'slider',
    components: {},
    props: {
      min: {
        type: Number,
        default: 0
      },
      max: {
        type: Number,
        default: 100
      },
      value: {
        type: [Array],
        default: () => ([0, 0])
      },
    }
    ,
    data() {
      return {
        firstValue: null,
        secondValue: null,
        dragging: false,
      }
    },
    mounted() { },
    methods: {
      emitChange() {
        this.$nextTick(() => {
          this.$emit('change', [this.minValue, this.maxValue]);
        });
      },
      setPosition(percent) {
        const targetValue = this.min + percent * (this.max - this.min) / 100;

        let button;
        if (Math.abs(this.value[0] - targetValue) < Math.abs(this.value[1] - targetValue)) {
          button = this.value[0] < this.value[1]? 'button1' : 'button2';
        } else {
          button = this.value[0] > this.value[1] ? 'button1' : 'button2';
        }
        this.$refs[button].setPosition(percent);
      },
      onSliderClick(event) {
        if (this.dragging) return;
        const sliderOffsetLeft = this.$refs.slider.getBoundingClientRect().left;
        this.setPosition((event.clientX - sliderOffsetLeft) * 100);
        this.emitChange();
      },
    },
    computed: {
      leftPosition() {
        return `${(this.value[0] - this.min) / (this.max - this.min) * 100}%`;
      },
      rightPosition() {
        return `${(this.value[1] - this.min) / (this.max - this.min) * 100}%`;
      },
      barSize() {
        return `${100 * (this.value[1] - this.value[0]) / (this.max - this.min)}%`
      },

      barStart() {
        return `${100 * (this.value[0] - this.min) / (this.max - this.min)}%`
      },

      barStyle() {
        return {
          width: this.barSize,
          left: this.barStart
        }
      },
    },
    watch: {}
  }
</script>

<style lang="less" scoped>
  .vtf-slider {
    .vtf-slider__runway {
      width: 100%;
      height: 6px;
      margin: 16px 0;
      background-color: #e4e7ed;
      border-radius: 3px;
      position: relative;
      cursor: pointer;
      vertical-align: middle;
    }
    .vtf-slider__bar {
      height: 6px;
      background-color: #409eff;
      border-top-left-radius: 3px;
      border-bottom-left-radius: 3px;
      position: absolute;
    }
    .vtf-slider__button-wrapper {
      height: 36px;
      width: 36px;
      position: absolute;
      z-index: 1001;
      top: -15px;
      transform: translateX(-50%);
      background-color: transparent;
      text-align: center;
      user-select: none;
      line-height: normal;
    }

    .vtf-slider__button-wrapper .vtf-tooltip,
    .vtf-slider__button-wrapper:after {
      display: inline-block;
      vertical-align: middle;
    }

    .vtf-slider__button {
      width: 16px;
      height: 16px;
      border: 2px solid #409eff;
      background-color: #fff;
      border-radius: 50%;
      transition: 0.2s;
      user-select: none;
    }
  }
</style>