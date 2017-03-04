<template>
  <div class="ryaudio">
    <div class="ryaudio-body">
      <div class="ryaudio-container" :style="`width: ${width}px;`">
        <audio :src="src" preload="true" ref="audio">
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
                <div class="ryaudio-progress" ref="progress">
                  <span class="ryaudio-progress-active" @click="setBackTo" :style="{width: activeProgress + '%'}"></span>
                  <input type="range" class="ryaudio-progress-bar" min="0" :max="progressMax" value="0" ref="slider" v-model="progressValue">
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
        progressValue: 0,
        isPlaying: false,
        progressMax: 0
      }
    },
    watch: {
      progressValue(val) {
        const audioEle = this.$refs.audio
        if (parseInt(val) !== parseInt(audioEle.currentTime)) {
          audioEle.currentTime = val
        }
      }
    },
    mounted() {
      const audioEle = this.$refs.audio

      audioEle.addEventListener('loadeddata', () => {
        this.progressMax = parseInt(audioEle.duration)
        this.duration = this.formatTime(audioEle.duration)
      })
      audioEle.addEventListener('timeupdate', () => {
        this.currentTime = this.formatTime(audioEle.currentTime)
        this.progressValue = audioEle.currentTime
      })
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
    computed: {
      activeProgress() {
        if (parseFloat(this.progressValue / this.progressMax) * 100 > 100) {
          return 100
        }
        return parseFloat(this.progressValue / this.progressMax) * 100
      }
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
      setBackTo(e) {
        const offsetX = e.offsetX
        const progressEle = this.$refs.progress
        const audioEle = this.$refs.audio
        const rect = progressEle.getBoundingClientRect()
        audioEle.currentTime = parseFloat(offsetX / rect.width) * audioEle.duration
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
}
.ryaudio-progress {
  flex: 1 1;
  display: flex;
  align-items: center;
  position: relative;
  z-index: 1200;
  .ryaudio-progress-active {
    position: absolute;
    left: 0;
    z-index: 1300;
    height: 2px;
    background-color: #1478F0;
    cursor: pointer;
  }
  input[type=range] {
    -webkit-appearance: none;
    width: 100%;
    background: #d2d2d2;
    height: 2px;
    margin: 0;
    padding: 0;
  }

  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
  }

  input[type=range]:focus {
    outline: none;
  }

  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    height: 15px;
    width: 15px;
    border: 1px solid #0078eb;
    box-shadow: 0 0 3px #0078eb;
    border-radius: 50%;
    background: #ffffff;
    cursor: pointer;
    margin-top: -7px;
    position: relative;
    z-index: 1400;
  }
  input[type=range]::-moz-range-thumb {
    height: 15px;
    width: 15px;
    border-radius: 50%;
    background: #ffffff;
    cursor: pointer;
    margin-top: -7px;
    position: relative;
    z-index: 1400;
    border: 1px solid #0078eb;
    box-shadow: 0 0 3px #0078eb;
  }
  input[type=range]::-ms-thumb {
    height: 15px;
    width: 15px;
    border-radius: 50%;
    background: #ffffff;
    cursor: pointer;
    margin-top: -7px;
    position: relative;
    z-index: 1400;
    border: 1px solid #0078eb;
    box-shadow: 0 0 3px #0078eb;
  }

  input[type=range]::-webkit-slider-runnable-track {
    width: 100%;
    height: 2px;
    cursor: pointer;
    background: #d2d2d2;
  }
  input[type=range]:focus::-webkit-slider-runnable-track {
    background: #d2d2d2;
  }
  input[type=range]::-moz-range-track {
    width: 100%;
    height: 2px;
    cursor: pointer;
    background: #d2d2d2;
  }
  input[type=range]::-ms-track {
    width: 100%;
    height: 2px;
    cursor: pointer;
    background: #d2d2d2;
    color: transparent;
  }
}
</style>
