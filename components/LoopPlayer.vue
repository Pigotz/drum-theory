<script setup>
import { ref } from 'vue'

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
    style="
      background: #6366f1;
      color: white;
      font-weight: 700;
      font-size: 14px;
      padding: 12px 16px;
      min-height: 44px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    "
  >
    {{ playing ? '⏹ Stop' : '▶ Play' }}
  </button>
</template>
