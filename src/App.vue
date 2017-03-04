<template>
  <div id="app">
    <div class="content">
      <div class="audio-item">
        <ry-audio :width="width" :title="source1.title" :src="source1.src"></ry-audio>
      </div>
      <div class="audio-item">
        <ry-audio :width="width" :title="source2.title" :src="source2.src"></ry-audio>
      </div>
      <div class="audio-item">
        <ry-audio :width="width" :title="source3.title" :src="source3.src"></ry-audio>
      </div>
    </div>
  </div>
</template>

<script>
import RyAudio from './components/RyAudio'

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
        title: 'Stay With Me - 朴灿烈/Punch',
        src: 'http://ocphdh79p.bkt.clouddn.com/staywithme.mp3'
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
body {
  margin: 0;
  padding: 0;
  background-color: #f2f2f2;
}
.audio-item {
  margin-top: 30px;
}
</style>
