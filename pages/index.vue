<script setup lang="ts">
const canvas = ref<HTMLCanvasElement | null>(null);
const x = ref(0);
const y = ref(0);

const snake = ref([
  { x: 5, y: 5 },
  { x: 6, y: 5 },
  { x: 7, y: 5 },
]);

onMounted(() => {
  if (!canvas.value) {
    return;
  }
  const ctx = canvas.value.getContext("2d");
  if (!ctx) return;
  for(const segment of snake.value){
    ctx.fillRect(segment.x * 20, segment.y * 20, 20, 20);
  }
});
</script>

<template>
  <div>
    <h1>hello its snake game</h1>
    <section>
      <canvas
        ref="canvas"
        width="400"
        height="400"
        class="w-[400px] h-[400px] border"
      />
    </section>
  </div>
</template>
