<template>
  <div id="app">
    <Audio></Audio>
    <Video></Video>
  </div>
</template>

<script>
import Audio from '@/components/Audio.vue'
import Video from '@/components/Video'

export default {
  name: 'Recorder',
  components: {
    Audio,
    Video
  }
}
</script>

<style lang="scss">
@import "~bootstrap/scss/bootstrap";

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#audio-recorder, #video-recorder {
  display: flex;
  justify-content: center;
  margin: .5em;
  padding: .5em;
  width: 500px;

  .recorder, .player {
    position: relative;
    display: inline-flex;
    align-items: center;

    .icon {
      width: 35px;
      height: 35px;
      fill: #747474;
      box-shadow: 0 2px 5px 1px rgba(158, 158, 158, 0.5);
      border-radius: 50%;
      cursor: pointer;
      transition: .2s;
      margin: .5em;

      &.icon-play, &.icon-stop {
        border: 1px solid #05CBCD;
        background-color: #FFFFFF;
      }

      &.icon-pause {
        fill: white;
        background-color: #FF6B64;
        border-color: transparent;
        animation: ripple .5s linear infinite;
        @keyframes ripple {
          0% {
            box-shadow: 0 0 0 0 rgba(red, 0.1),
            0 0 0 1px rgba(red, 0.1),
            0 0 0 5px rgba(red, 0.1);
          }
          100% {
            box-shadow: 0 0 0 0 rgba(red, 0.1),
            0 0 0 10px rgba(red, 0.1),
            0 0 0 20px rgba(red, 0);
          }
        }
      }

      &.paused {
        fill: white;
        background-color: #FF6B64;
        border-color: transparent;

        &:hover {

        }
      }

      &:hover {
        fill: #505050;
      }
    }
  }

  .player {
    margin-left: auto;
  }
}
</style>
