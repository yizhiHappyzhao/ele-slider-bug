<template>
  <div class="threshold-setting">
    <el-slider
      tooltip-class="epm-threshold__tips"
      ref="slider"
      v-model="sliderValue"
      range
      :data-min-value="`${minValue}%`"
      :data-max-value="`${maxValue}%`"
      :step="sliderStep"
      :min="minValue"
      :max="maxValue"
      :show-tooltip="showTooltip"
      @input="changeSliderAddedElWidth"
      @change="changeSliderBtnColor"
    ></el-slider>
  </div>
</template>

<script>
export default {
  props: {
    defaultRange: {
      type: Array,
      default: () => {
        return [-50, 80]
      }
    },
    step: {
      type: Number,
      default: 1
    },
    minValue: {
      type: Number,
      default: -100
    },
    maxValue: {
      type: Number,
      default: 100
    }
  },
  data () {
    return {
      sliderValue: this.defaultRange,
      sliderStep: this.step,
      sliderBarEl: null,
      sliderSonEl: null,
      showTooltip: true,
      tipsSpan0: null,
      tipsSpan1: null,
      tipsNodes: [],
      sliderBtnWarpNodes: [],
      colorGreen: '#45b690',
      colorRed: '#D44239',
      colorYellow: '#fea616',
      isEquality: false,
      firstBtnWarpLeftCopy: null,
      secondBtnWarpLeftCopy: null
    }
  },
  mounted () {
    _addSonELForSliderEl(this)
  },
  methods: {
    changeSliderAddedElWidth (val) {
      this.$nextTick(() => {
        console.log(111)
        const left = this.sliderBarEl.style.left
        this.sliderSonEl.style.setProperty('--width', left)
        if (this.tipsNodes[0]) {
          this.tipsSpan0.innerText = this.tipsNodes[0].innerText
          this.tipsSpan1.innerText = this.tipsNodes[1].innerText
        }
        const firstBtnWarpLeft = parseFloat(this.sliderBtnWarpNodes[0].style.left.replace('%', ''))
        const secondBtnWarpLeft = parseFloat(this.sliderBtnWarpNodes[1].style.left.replace('%', ''))
        if (firstBtnWarpLeft === secondBtnWarpLeft) {
          this.isEquality = true
          this.firstBtnWarpLeftCopy = firstBtnWarpLeft
          this.secondBtnWarpLeftCopy = secondBtnWarpLeft
        }
        if (this.firstBtnWarpLeftCopy && this.firstBtnWarpLeftCopy < firstBtnWarpLeft) {
          this.sliderBtnWarpNodes[1].style.display = 'none'
          this.sliderValue[0] = this.sliderValue[1]
          this.sliderBarEl.style.setProperty('--color', this.colorGreen)
        }
        if (this.firstBtnWarpLeftCopy && this.firstBtnWarpLeftCopy > firstBtnWarpLeft) {
          this.sliderBtnWarpNodes[1].style.display = 'flex'
          this.sliderBarEl.style.setProperty('--color', this.colorYellow)
        }
        if (this.secondBtnWarpLeftCopy && this.secondBtnWarpLeftCopy > secondBtnWarpLeft) {
          this.sliderBtnWarpNodes[0].style.display = 'none'
          this.sliderValue[1] = this.sliderValue[0]
          this.sliderBarEl.style.setProperty('--color', this.colorRed)
        }
        if (this.secondBtnWarpLeftCopy && this.secondBtnWarpLeftCopy < secondBtnWarpLeft) {
          this.sliderBtnWarpNodes[0].style.display = 'flex'
          this.sliderBarEl.style.setProperty('--color', this.colorYellow)
        }
      })
    },
    changeSliderBtnColor () {
      this.firstBtnWarpLeftCopy = null
      this.secondBtnWarpLeftCopy = null
    }
  }
}
function _addSonELForSliderEl (that) {
  that.$nextTick(() => {
    const parentEl = document.querySelector('.el-slider__runway')
    that.sliderSonEl = document.createElement('div')
    parentEl.appendChild(that.sliderSonEl)
    that.sliderBarEl = document.querySelector('.el-slider__bar')
    const left = that.sliderBarEl.style.left
    that.sliderSonEl.style.setProperty('--width', left)
    that.sliderBarEl.style.setProperty('--color', that.colorYellow)
    that.sliderSonEl.className = 'son'
    that.$refs.slider.$children.forEach((children) => {
      children.displayTooltip()
    })
    for (let i = 0; i <= 1; i++) {
      that[`tipsSpan${i}`] = document.createElement('span')
      that[`tipsSpan${i}`].className = 'tips'
      that[`tipsSpan${i}`].innerText = that.sliderValue[i]
      that.$refs.slider.$refs[`button${i + 1}`].$el.appendChild(that[`tipsSpan${i}`])
    }
    that.sliderBtnWarpNodes = document.querySelectorAll('.el-slider__button-wrapper')
    that.sliderBtnWarpNodes[0].style.setProperty('--color', that.colorGreen)
    that.sliderBtnWarpNodes[1].style.setProperty('--color', that.colorRed)
    setTimeout(() => {
      that.tipsNodes = document.querySelectorAll('.epm-threshold__tips')
    }, 0)
  })
}
</script>

<style lang='scss' scoped>
.threshold-setting {
  width: 480px;
  height: 100%;
  padding: 16px 60px;
  box-sizing: border-box;
  ::v-deep .el-slider{
    position: relative;
    &::after{
      content: attr(data-max-value);
      width: auto;
      height: auto;
      font-size: 12px;
      color: #182A4E;
      position: absolute;
      right: 0;
      bottom: -25px;
    }
    &::before{
      content: attr(data-min-value);
      width: auto;
      height: auto;
      font-size: 12px;
      color: #182A4E;
      position: absolute;
      left: 0;
      bottom: -25px;
    }
  }
  ::v-deep .el-slider__runway {
    height: 10px;
    border-radius: 0;
    background-color: #d44237;
    position: relative;
  }
  ::v-deep .el-slider__bar {
    height: 10px;
    background: var(--color);
  }
  ::v-deep .el-slider__button-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    &:hover,
    &.hover {
      cursor: pointer;
    }
    &::after {
      width: 6px;
      height: 9px;
      background: #182a4e;
      border-radius: 2px;
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      margin-top: 2px;
    }
    &:nth-child(2){
      &::after{
        background: var(--color);
      }
    }
    &:nth-child(3){
      &::after{
        background: var(--color);
      }
    }
  }
  ::v-deep .el-slider__button {
    width: 16px;
    height: 16px;
    border: none;
    border-radius: 0;
    box-shadow: 0px -1px 4px 0px rgba(0, 0, 0, 0.5);
    margin-top: 7px;
    position: relative;
    &:hover,
    &.hover,
    &.dragging {
      cursor: pointer;
      transform: none;
    }
    &::after {
      content: "";
      position: absolute;
      left: 50%;
      top: -6px;
      transform: translateX(-50%);
      width: 0;
      height: 0;
      border-width: 0 9px 6px;
      border-style: solid;
      border-color: transparent transparent rgb(253, 253, 253);
      filter: drop-shadow(0px 0px 0 rgba(0, 0, 0, 0.5));
    }
  }
  ::v-deep .son {
    height: 10px;
    width: var(--width);
    position: absolute;
    top: 0;
    left: -var(--width);
    background-color: #45b690;
  }
  ::v-deep .tips {
      width: auto;
      height: auto;
      position: absolute;
      top: -15px;
      left: 50%;
      transform: translateX(-50%);
      color: #182A4E;
      font-size: 16px;
    }
}
</style>
<style lang="scss">
.epm-threshold__tips{
  opacity: 0;
}
</style>
