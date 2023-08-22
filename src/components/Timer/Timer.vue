<template>
  <h4>
      Remaining time is:
    {{ time }}
  </h4>
</template>

<script setup lang="ts">
import { useIntervalFn } from '@vueuse/core'
import { ref} from 'vue'
import type { Props } from './timer.types.ts'

const props = defineProps<Props>()

// TIMER
const time = ref(props.time)

const passTime = () => (time.value -= 1)

function stopPassingTime() {
  if (time.value <= 0) {
    return
  }

  passTime()
}

useIntervalFn(stopPassingTime, 1000)

</script>
