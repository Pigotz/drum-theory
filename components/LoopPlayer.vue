<script setup>
import { ref } from 'vue'
import { onSlideLeave } from '@slidev/client'

const audio = ref(null)
const playing = ref(false)

const src = import.meta.env.BASE_URL + 'backing.mp3'

const toggle = () => {
  if (playing.value) {
    audio.value.pause()
  } else {
    audio.value.play()
  }
  playing.value = !playing.value
}

onSlideLeave(() => {
  if (playing.value) {
    audio.value.pause()
    playing.value = false
  }
})
</script>

<template>
  <audio
    ref="audio"
    :src="src"
    loop
  />
  <button
    @click="toggle"
    :aria-label="playing ? 'Stop backing track' : 'Play backing track'"
    :style="{
      background: playing ? '#4b5563' : '#e2e8f0',
      color: playing ? '#e2e8f0' : '#0f1117',
      fontWeight: '700',
      fontSize: '14px',
      padding: '12px 24px',
      minHeight: '44px',
      border: 'none',
      borderRadius: '6px',
      cursor: 'pointer',
    }"
  >
    {{ playing ? 'Stop' : 'Play' }}
  </button>
</template>
