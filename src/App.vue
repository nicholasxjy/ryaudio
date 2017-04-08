<template>
  <div id="app">
    <div class="content">
      <div class="audio-item">
        <p>
          <ry-audio :width="width" :title="source1.title" :src="source1.src"></ry-audio>
        </p>
      </div>
      <div class="audio-item">
        <ry-audio :width="width" :title="source2.title" :src="source2.src"></ry-audio>
      </div>
      <div class="audio-item">
        <p>
          <span id="audio-container"></span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import RyAudio from './components/RyAudio.vue'
import Vue from 'vue'

export default {
  name: 'app',
  components: {
    RyAudio
  },
  data() {
    return {
      width: 375,
      source1: {
        title: '红色高跟鞋 - 蔡健雅',
        src: 'http://ocphdh79p.bkt.clouddn.com/hsggx.mp3'
      },
      source2: {
        title: '词不达意 - 林忆莲',
        src: 'http://ocphdh79p.bkt.clouddn.com/cbdy.mp3'
      },
      source3: {
        title: '第2集：打开经济指标黑匣子',
        src: 'https://premium.wallstcn.com/%E5%87%8C%E9%B9%8F%E7%AC%AC%E4%BA%8C%E8%AE%B2%EF%BC%88%E4%BF%AE%E6%AD%A3%E7%89%88%EF%BC%89%EF%BC%9A%E6%8C%87%E6%A0%87%E4%BD%93%E7%B3%BB%E7%9A%84%E5%A5%A5%E4%B9%89.mp3'
      }
    }
  },
  mounted() {
    const vm = this
    const el = document.querySelector('#audio-container')
    if (el) {
      const com = new Vue({
        render(h) {
          return h(RyAudio, {
            props: {
              src: vm.source3.src,
              title: vm.source3.title,
              width: vm.width
            }
          })
        }
      }).$mount(el)
    }
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Oxygen,Ubuntu,Cantarell,Open Sans,Helvetica Neue,sans-serif;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  -moz-font-feature-settings: "liga" on;
  font-size: 14px;
  font-weight: 400;
  font-style: normal;
  line-height: 1.4;
  letter-spacing: 0;
  color: #4a4a4a;
  background-color: #fafafa;
  word-break: break-all;
}
.audio-item {
  margin-top: 30px;
}
</style>
