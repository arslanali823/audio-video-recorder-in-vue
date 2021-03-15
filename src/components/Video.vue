<template>
  <div class="attachmentFile justify-content-center align-items-center" id="video-recorder">
    <div class="recorder">
      <div class="icon icon-play" :class="{'paused': isPaused}" v-if="status === 'play'" v-html="play"
           @click="toggleVideo"></div>
      <div class="icon icon-pause" v-if="status === 'pause'" v-html="pause" @click="pauseVideo"></div>
      <div class="icon icon-stop" v-html="stop" @click="stopVideo"></div>
    </div>
    <div class="player" id="video-player">
      <video width="320" height="240" class="video" id="video" controls/>
      <img src="../assets/cancel.svg" alt="close" id="attch-close" class="cancelImg img-fluid">
    </div>
  </div>
</template>

<script>
import RecordRTC from 'recordrtc'
import Axios from 'axios'

export default {
  name: 'Video',
  data() {
    return {
      play: `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 14c1.66 0 2.99-1.34 2.99-3L15 5c0-1.66-1.34-3-3-3S9 3.34 9 5v6c0 1.66 1.34 3 3 3zm5.3-3c0 3-2.54 5.1-5.3 5.1S6.7 14 6.7 11H5c0 3.41 2.72 6.23 6 6.72V21h2v-3.28c3.28-.48 6-3.3 6-6.72h-1.7z"/><path d="M0 0h24v24H0z" fill="none"/></svg>`,
      pause: `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z"/><path d="M0 0h24v24H0z" fill="none"/></svg>`,
      stop: `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M0 0h24v24H0z" fill="none"/><path d="M6 6h12v12H6z"/></svg>`,
      status: 'play',
      isRecording: false,
      isPaused: false,
      recorder: null,
      constraints: {
        video: true,
        audio: true
      },
      streaming: null,
      chunks: []
    }
  },
  methods: {
    // Toggle Video between start pause
    toggleVideo() {
      if (!this.isRecording || (this.isRecording && this.isPaused)) {
        navigator.mediaDevices.getUserMedia(this.constraints)
            .then(async stream => {
              let recorderRTC = RecordRTC(stream, {
                type: 'video',
                mimeType: 'video/mp4',
                timeSlice: 1000
              });

              recorderRTC.startRecording();

              const sleep = m => new Promise(r => setTimeout(r, m));
              await sleep(1);
              this.recorder = recorderRTC
              this.streaming = stream
              this.isRecording = true
              this.status = 'pause'
              let video = document.getElementById('video')
              if (stream instanceof MediaStream) {
                video.srcObject = stream
              }
              video.muted = true;
              video.autoplay = true
            });
      } else {
        this.recorder.resumeRecording()
        this.isPaused = false
      }
    },
    pauseVideo() {
      this.recorder.pauseRecording()
      this.isPaused = true
      this.status = 'play'
    },
    stopVideo() {
      this.recorder.stopRecording(() => {
        let blob = this.recorder.getBlob()
        this.status = 'play'
        this.isRecording = false
        this.isPaused = false

        document.getElementById('video').remove()
        const videoParentNode = document.getElementById('video-player')
        const video = document.createElement('video')
        video.id = 'video'
        video.controls = true
        video.width = 320
        video.height = 240
        video.src = URL.createObjectURL(blob)
        videoParentNode.prepend(video)

        let fileName = Math.random().toString().substr(2) + '.mp4'
        let formData = new FormData();

        let file = new File([blob], fileName, {type: 'video/mp4'})
        formData.append('attachment', file, fileName);
        Axios.post('http://apc.local/api/attachment', formData)
            .then(resp => {
              console.log(resp)
              this.streaming.getTracks().forEach((track) => {
                track.stop()
              })
            }).catch(error => {
          console.log(error)
        })
      })
    },
  }
}
</script>

<style scoped>

</style>