<template>
  <div>
    <canvas ref="myCanvas"> </canvas>

    <div>detected pitch: {{ detectedPitch }}</div>
  </div>
</template>
<script>
import * as PitchFinder from 'pitchfinder'
import { ref } from 'vue'
export default {
  data() {
    return {
      detectedPitch: 0,
      targetPitch: 130.8
    }
  },
  mounted() {
    //this.detectedPitch = ref(0)
    this.setupPitchDetection()
  },
  methods: {
    setupPitchDetection() {
      navigator.mediaDevices
        .getUserMedia({ audio: true })
        .then((stream) => {
          const audioContext = new AudioContext()
          const source = audioContext.createMediaStreamSource(stream)
          const scriptNode = audioContext.createScriptProcessor(1024, 1, 1)

          scriptNode.onaudioprocess = (event) => {
            const audioData = event.inputBuffer.getChannelData(0)

            const detectPitch = PitchFinder.AMDF()
            const pitch = detectPitch(audioData) // null if pitch cannot be identified
            if (pitch) {
              this.detectedPitch = pitch
              console.log(this.detectedPitch)
              this.drawTuningArm(pitch)
            }
          }

          source.connect(scriptNode)
          scriptNode.connect(audioContext.destination)
        })
        .catch((error) => {
          console.error('Error accessing microphone:', error)
        })
    },
    drawTuningArm(pitch) {
      const lowerPitch = 100.47
      const upperPitch = 150.813
      const pitchRange = upperPitch - lowerPitch
      const height = 150
      const topWidth = 300
      let drawValue = 0
      if (pitch < lowerPitch) {
        drawValue = 0
      } else if (pitch > upperPitch) {
        drawValue = topWidth
      } else {
        drawValue = topWidth * ((pitch - lowerPitch) / pitchRange)
      }
      this.drawTrapezoid()
      const canvas = this.$refs.myCanvas
      const xCenter = canvas.width / 2
      const ctx = canvas.getContext('2d')
      // Start a new Path
      ctx.beginPath()
      ctx.moveTo(drawValue, 0)
      ctx.lineTo(xCenter, height)

      // Draw the Path
      ctx.stroke()
      ctx.closePath()
    },
    drawTrapezoid() {
      const canvas = this.$refs.myCanvas
      const ctx = canvas.getContext('2d')

      // Clear canvas
      ctx.clearRect(0, 0, canvas.width, canvas.height)

      // Set trapezoid properties
      const topWidth = 300
      const bottomWidth = 200
      const height = 150
      const xCenter = canvas.width / 2
      const yCenter = canvas.height / 2

      // Begin drawing path
      ctx.beginPath()

      // Draw trapezoid
      // Draw trapezoid
      ctx.moveTo(xCenter - topWidth / 2, yCenter - height / 2) // Top-left corner
      ctx.lineTo(xCenter + topWidth / 2, yCenter - height / 2) // Top-right corner
      ctx.lineTo(xCenter + bottomWidth / 2, yCenter + height / 2) // Bottom-right corner
      ctx.lineTo(xCenter - bottomWidth / 2, yCenter + height / 2) // Bottom-left corner

      // Close the path
      ctx.closePath()
      // Fill the trapezoid with a color
      ctx.fillStyle = 'grey'
      ctx.fill()

      ctx.fillStyle = 'green'
      ctx.fillRect(xCenter - 10, 0, 20, height)
    }
  }
}
</script>

<style>
canvas {
  border: 1px solid black;
}
</style>
