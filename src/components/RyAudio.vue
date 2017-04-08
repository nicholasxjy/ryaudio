<template>
  <div class="ryaudio">
    <div class="ryaudio-body">
      <div class="ryaudio-container" :style="`width: ${width}px;`">
        <audio preload="auto" ref="audio">
          <p>we believe you need to upgrade your browser :)</p>
        </audio>
        <div class="ryaudio-controls">
          <div class="ryaudio-controls-inner">
            <div class="ryaudio-controls-play-pause">
              <a class="ryaudio-controls-play" @click="onAudioPlay" v-if="!isPlaying"></a>
              <a class="ryaudio-controls-pause" @click="onAudioPause" v-if="isPlaying"></a>
            </div>
            <div class="ryaudio-controls-content">
              <div class="ryaudio-title">{{title}}</div>
              <div class="ryaudio-controls-content-main">
                <span class="ryaudio-controls-currenttime">{{currentTime}}</span>
                <div class="ryaudio-progress">
                  <span class="ryaudio-progress-active" @click="setBackTo" :style="{width: activeProgress + 'px'}"></span>
                  <div class="ryaudio-progress-bar" ref="progress" @click="setBackTo"></div>
                  <span class="ryaudio-progress-slider" ref="slider" :style="{left: sliderLeft + 'px'}" @touchstart="handleSliderDown" @mousedown="handleSliderDown"></span>
                </div>
                <span class="ryaudio-duration">{{duration}}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'RyAudio',
    props: {
      src: {
        type: String,
        default: 'http://7xj610.com1.z0.glb.clouddn.com/The%20Cardigans%20-%20Communication.mp3'
      },
      title: String,
      width: {
        type: Number,
        default: 320
      },
      onPlay: {
        type: Function
      },
      onPause: {
        type: Function
      }
    },
    data() {
      return {
        currentTime: '00:00',
        duration: '00:00',
        activeProgress: 0,
        sliderLeft: 0,
        isPlaying: false
      }
    },
    mounted() {
      const audioEle = this.$refs.audio

      audioEle.src = this.src
      audioEle.preload = 'auto'

      audioEle.addEventListener('loadeddata', this.handleLoadedData, false)
      audioEle.addEventListener('timeupdate', this.handleTimeUpdate, false)

      audioEle.addEventListener('play', () => {
        if (!this.isPlaying) {
          this.isPlaying = true
        }
      })
      audioEle.addEventListener('pause', () => {
        if (this.isPlaying) {
          this.isPlaying = false
        }
      })
    },
    beforeDestroy() {
      document.removeEventListener('mouseup', this.handleSliderUp, false)
      document.removeEventListener('touchend', this.handleSliderUp, false)
      document.removeEventListener('mousemove', this.handleSliderMove, false)
      document.removeEventListener('touchmove', this.handleSliderMove, false)
    },
    methods: {
      onAudioPlay() {
        if (this.onPlay && typeof this.onPlay === 'function') {
          this.onPlay(this.$refs.audio)
        } else {
          this.$refs.audio.play()
          this.isPlaying = true
        }
      },
      onAudioPause() {
        if (this.onPause && typeof this.onPause === 'function') {
          this.onPause(this.$refs.audio)
        } else {
          this.$refs.audio.pause()
          this.isPlaying = false
        }
      },
      formatTime(num) {
        const temp = parseFloat(num)
        let secs = parseInt(temp % 60)
        let mins = parseInt((temp / 60) % 60)
        secs = `0${secs}`.slice(-2)
        mins = `0${mins}`.slice(-2)
        return `${mins}:${secs}`
      },
      calculateSliderLeft() {
        const sliderEle = this.$refs.slider
        if (sliderEle) {
          const sliderRect = sliderEle.getBoundingClientRect()
          if (this.activeProgress > sliderRect.width / 2) {
            return this.activeProgress - sliderRect.width / 2
          }
        }
        return 0
      },
      setBackTo(e) {
        const offsetX = e.offsetX
        const progressEle = this.$refs.progress
        const audioEle = this.$refs.audio
        const rect = progressEle.getBoundingClientRect()
        audioEle.currentTime = parseFloat(offsetX / rect.width) * audioEle.duration
      },
      handleLoadedData() {
        const audioEle = this.$refs.audio
        this.duration = this.formatTime(audioEle.duration)
      },
      handleTimeUpdate() {
        const audioEle = this.$refs.audio
        const progressEle = this.$refs.progress
        const progressRect = progressEle.getBoundingClientRect()
        const currentTime = audioEle.currentTime
        const duration = audioEle.duration
        this.activeProgress = parseFloat(currentTime / duration) * progressRect.width
        this.sliderLeft = this.calculateSliderLeft()
        this.currentTime = this.formatTime(currentTime)
      },
      handleSliderDown() {
        // here need to bind the mousemove touchmove to document
        document.addEventListener('mousemove', this.handleSliderMove, false)
        document.addEventListener('touchmove', this.handleSliderMove, false)

        // add remove listener
        document.addEventListener('mouseup', this.handleSliderUp, false)
        document.addEventListener('touchend', this.handleSliderUp, false)
      },
      handleSliderUp() {
        document.removeEventListener('mousemove', this.handleSliderMove, false)
        document.removeEventListener('touchmove', this.handleSliderMove, false)
      },
      handleSliderMove(e) {
        // here should handle mouse and touch type events
        let pageX
        if (e.type === 'mousemove') {
          pageX = e.pageX
        }
        if (e.type === 'touchmove') {
          pageX = e.touches[0].pageX
        }
        const audioEle = this.$refs.audio
        const progressEle = this.$refs.progress
        const rect = progressEle.getBoundingClientRect()
        let offset = pageX - rect.left
        if (offset < 0) {
          offset = 0
        }
        if (offset > rect.width) {
          offset = rect.width
        }
        this.activeProgress = offset
        this.sliderLeft = this.calculateSliderLeft()
        audioEle.currentTime = parseFloat(offset / rect.width) * audioEle.duration
      }
    }
  }
</script>

<style lang="scss" scoped>
.ryaudio-body {
  height: 60px;
  background-color: #fff;
}
.ryaudio-controls {
  width: 100%;
}
.ryaudio-controls-inner {
  display: flex;
  align-items: center;
  height: 60px;
  padding-left: 15px;
  padding-right: 15px;
}
.ryaudio-controls-play-pause {
  width: 40px;
}
.ryaudio-controls-content {
  flex: 1 1;
  padding-left: 10px;
}
.ryaudio-controls-content-main {
  flex: 1 1;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}
.ryaudio-title {
  width: 100%;
  font-size: 14px;
  font-weight: 600;
  line-height: 1.4;
  color: #4a4a4a;
  padding-left: 10px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-bottom: 5px;
}
.ryaudio-controls-play, .ryaudio-controls-pause {
  display: block;
  width: 40px;
  height: 40px;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  cursor: pointer;
  outline: none;
}
.ryaudio-controls-play {
  background-image: url('../assets/play.svg');
}
.ryaudio-controls-pause {
  background-image: url('../assets/pause.svg');
}
.ryaudio-controls-currenttime, .ryaudio-duration {
  font-size: 12px;
  padding-left: 10px;
  padding-right: 10px;
  color: #4a4a4a;
  width: 35px;
}
.ryaudio-progress {
  flex: 1 1;
  display: flex;
  align-items: center;
  position: relative;
  z-index: 1200;
  height: 20px;
}
.ryaudio-progress-active {
  position: absolute;
  top: 50%;
  left: 0;
  z-index: 1300;
  height: 2px;
  background-color: #1478F0;
  cursor: pointer;
  transform: translateY(-50%);
}
.ryaudio-progress-bar {
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  height: 2px;
  background-color: #d2d2d2;
  width: 100%;
  z-index: 1250;
  cursor: pointer;
}
.ryaudio-progress-slider {
  position: absolute;
  top: 50%;
  left: 0;
  display: block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #fff;
  border: 1px solid #2772DB;
  transform: translateY(-50%);
  z-index: 1400;
}
</style>
