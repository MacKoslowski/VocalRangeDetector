<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <!-- Header -->
        <v-app-bar app>
          <!-- Add your header content here -->
          TEst
        </v-app-bar>

        <!-- Content -->
        <v-row justify="center" align="center" class="my-6">
          <!-- Pitch detection circle -->
          <v-card elevation="12" class="text-center">
            <v-card-title>
              <h2>Pitch Detection</h2>
              <pitch-detector />
              <div>{{ currentRange }}</div>
              <v-btn @click="test"> Start</v-btn>
            </v-card-title>
            <v-card-text>
              <!-- Add your pitch detection circle component here -->
            </v-card-text>
          </v-card>

          <!-- Ads -->
          <v-card class="mt-6">
            <!-- Add your ad content here -->
          </v-card>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import {
  BassNotes,
  BaritoneNotes,
  TenorNotes,
  CounterTenorNotes,
  ContraltoNotes,
  MezzoSopranoNotes,
  SopranoNotes
} from '../util/VocalUtil'
import * as Tone from 'tone'
import PitchDetectorVue from './PitchDetector.vue'
import PitchDetector from './PitchDetector.vue'
export default {
  components: { PitchDetector },
  data() {
    return {
      currentRange: ''
    }
  },
  mounted() {},
  methods: {
    playVocalRange(synth, vocalRange) {
      const now = Tone.now()
      let timeDiff = 0
      for (let i = 0; i < vocalRange.length; i++) {
        let note = vocalRange[i]
        console.log(note)
        synth.triggerAttackRelease(note, '8n', now + timeDiff)
        timeDiff += 0.5
      }
    },
    test() {
      const synth = new Tone.Synth().toDestination()
      this.currentRange = 'Bass'
      this.playVocalRange(synth, BassNotes)
    }
  }
}
</script>
