<template>
  <div class="outer">
    <div class="flap" id="flap"></div>
    <div class="flap top"></div>
    <div class="flap bottom"></div>
    <div class="gap"></div>
  </div>

  <div v-for="(text, index) in flaps" :key="index">
    <input v-model="flaps[index]" :placeholder="'Text ' + (index + 1)" />
  </div>

  <button @click="start">Start</button>
  <button @click="slow">Slow</button>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const flaps = ref<string[]>(['', '', '', '', '', '', '', '', '', '']);

function start() {
  turn(60);
}

function slow() {
  turn(300);
}

async function turn(duration: number) {
  for (const text of flaps.value) {
    if (text) {
      await turnFlap(text, duration);
    }
  }
}

async function turnFlap(nextMsg: string, duration: number) {
  const flap = document.querySelector('#flap') as HTMLElement;
  const flapT = document.querySelector('.flap.top') as HTMLElement;
  const flapB = document.querySelector('.flap.bottom') as HTMLElement;

  flapT.innerHTML = nextMsg;
  flap.style.zIndex = '11';
  flapB.style.zIndex = '12';
  flapT.style.zIndex = '10';
  flapT.style.display = 'block';
  flapB.style.display = 'block';

  let animation = flap.animate([{ transform: 'rotateX(-90deg)' }], {
    duration: duration,
  });
  await animation.finished;

  flap.style.transform = 'rotateX(90deg)';
  flap.innerHTML = nextMsg;
  flap.style.zIndex = '11';
  flapB.style.zIndex = '10';
  flapT.style.zIndex = '12';

  animation = flap.animate([{ transform: 'rotateX(0deg)' }], {
    duration: duration,
    fill: 'forwards',
  });
  await animation.finished;
  animation.commitStyles();
  animation.cancel();

  flapB.innerHTML = flap.innerHTML;
  flapB.style.display = 'none';
  flapT.style.display = 'none';
}
</script>