<template>
  <div class="timeline-filter">
    <svg style="position:absolute;z-index: -1;" class="timeline-filter-gradient" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <linearGradient id="timeline-filter-selected-gradient" gradientUnits="userSpaceOnUse" x1="0" y1="0" x2="0" y2="100%">
          <stop offset="0" stop-color="#0d71ba"></stop>
          <stop offset="0.7" stop-color="#1fb597"></stop>
          <stop offset="1" stop-color="#1eb194"></stop>
        </linearGradient>
        <linearGradient id="timeline-filter-hovered-gradient" gradientUnits="userSpaceOnUse" x1="0" y1="0" x2="0" y2="100%">
          <stop offset="0" stop-color="#0c62a5"></stop>
          <stop offset="0.7" stop-color="#1c9a7f"></stop>
        </linearGradient>
      </defs>
    </svg>
    <div class="histogram">
      <svg style="width:100%" xmlns="http://www.w3.org/2000/svg">
        <g @mouseover="onMouseover($event,index)" @mouseout="hover=null" @click="onGClick(index)" :class="{'bar-group':true,selected:index>=value[0]&&index<=value[1]}" :key="index" v-for="(item,index) in data">
          <rect class="bar-bg" :x="item.x+'%'" y="0" :width="item.width+'%'" height="100%"></rect>
          <rect class="bar" :x="item.x+'%'" :y="(100-item.y*100)+'%'" :width="item.width+'%'" :height="item.y*100+'%'"></rect>
        </g>
      </svg>
      <div v-if="hover" class="hint" :style="{left: hover.box.x+hover.box.width/2+'px'}">
        <span class="year">{{hover.target.label}} : </span>
        <span class="count">{{hover.target.value}}</span>
      </div>
    </div>
    <slot name="slider" :value="value" :data="data" ></slot>

  </div>

</template>

<script>
  export default {
    name: 'vue-timeline-filter-component',
    components: {},
    props: ['list'],
    data() {
      return {
        value: [0, 0],
        hover: null
      }
    },
    created() {
    },
    mounted() {

    },
    methods: {
      fastYear(value) {
        this.value = [Math.max(0, this.data.length - 1 - value), this.data.length - 1]
        this.emit()
      },
      reset() {
        this.value = [0, this.data.length - 1]
        this.emit()
      },
      emit() {
        this.$emit('change', [this.data[this.value[0]], this.data[this.value[1]]])
      },
      onSliderChange() {
        this.emit()
      },
      onMouseover(event, index) {
        let { target } = event
        this.hover = { box: target.getBBox(), target: this.data[index] }
        console.log(this.hover)
      },
      formatTooltip(i) {
        if (this.data && this.data[i]) {
          const { label, value } = this.data[i]
          return `${label} : ${value}`
        }
      },
      onGClick(index) {
        if (Math.abs(this.value[1] - index) < Math.abs(this.value[0] - index)) {
          this.value = [this.value[0], index]
        } else {
          this.value = [index, this.value[1]]
        }
        this.emit()
      },
    },
    computed: {
      data() {
        let res = []
        if (this.list) {
          let max = -Infinity
          let min = Infinity
          let width = 100 / this.list.length
          this.list.forEach(item => {
            const { value = 0, label } = item
            if (label) {
              if (value >= max) {
                max = value
              }
              if (value <= min) {
                min = value
              }
            }
          })
          this.list.forEach((item, index) => {
            const { value = 0, label } = item
            if (label) {
              res.push({
                value,
                label,
                x: index * width,
                width,
                y: Math.max(0.02, value / max)
              })
            }
          })
          return res
        }
        return res
      }
    },
    watch: {
      data: {
        handler(val) {
          this.value = [0, val.length - 1]
        }, immediate: true
      }
    }
  }
</script>

<style lang="less" scoped>
  .bar-group {
    cursor: pointer;
    &.selected .bar {
      fill: url(#timeline-filter-selected-gradient);
    }
    .bar-bg {
      fill: transparent;
    }
    .bar {
      fill: rgba(0, 0, 0, 0.15);
      stroke: #ffffff;
      stroke-width: 0.4;
    }
  }
  .histogram:not(.inactive) .bar-group:hover {
    .bar {
      fill: url(#timeline-filter-hovered-gradient);
    }
  }
  .histogram {
    position: relative;
  }
  .hint {
    position: absolute;
    top: 100%;
    z-index: 10;
    margin-top: 9px;
    background-color: rgba(33, 33, 33, 0.9);
    color: #ffffff;
    padding: 6px 10px;
    font-size: 14px;
    line-height: 20px;
    white-space: nowrap;
    text-align: center;
    transform: translateX(-50%);
    border-radius: 4px;
    &::before {
      content: "";
      display: block;
      width: 10px;
      height: 10px;
      position: absolute;
      left: 50%;
      bottom: 100%;
      background: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxMCIgaGVpZ2h0PSIxMCI+CiAgPHBvbHlnb24gZmlsbD0iIzIxMjEyMSIgcG9pbnRzPSIwLDEwIDUsNSAxMCwxMCIvPgo8L3N2Zz4K)
        no-repeat center bottom/10px;
      opacity: 0.9;
      transform: translateX(-50%);
    }
  }
  /deep/.el-slider__bar {
    background: #1ca186;
  }
  /deep/.el-slider__button {
    border-color: #1ca186;
  }
</style>