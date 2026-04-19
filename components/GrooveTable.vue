<script setup>
defineProps({
  groove: {
    type: Object,
    default: () => ({
      hh: [1, 1, 1, 1, 1, 1, 1, 1],
      sn: [0, 0, 1, 0, 0, 0, 1, 0],
      kk: [1, 0, 0, 0, 1, 0, 0, 0],
    }),
  },
  beats: {
    type: Array,
    default: () => ['1', '+', '2', '+', '3', '+', '4', '+'],
  },
})

const COLORS = { hh: '#eab308', sn: '#3b82f6', kk: '#ef4444' }
const LABELS = { hh: 'HH', sn: 'SN', kk: 'KK' }
const ROWS = ['hh', 'sn', 'kk']
</script>

<template>
  <div style="display: flex; align-items: center; justify-content: center; height: 100%;">
    <table style="border-collapse: collapse; width: 100%;">
      <thead>
        <tr>
          <th style="color: white; font-weight: 700; padding: 8px; text-align: left; font-size: 14px;"></th>
          <th
            v-for="beat in beats"
            :key="beat"
            style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;"
          >{{ beat }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in ROWS" :key="row">
          <td style="color: white; font-weight: 700; padding: 8px; font-size: 14px;">{{ LABELS[row] }}</td>
          <td
            v-for="(hit, i) in groove[row]"
            :key="i"
            :style="hit ? `background: ${COLORS[row]}; padding: 8px; min-width: 24px;` : 'padding: 8px; min-width: 24px;'"
          ></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
