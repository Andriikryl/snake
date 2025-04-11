<script setup lang="ts">
type Direction = "right" | "left" | "up" | "down";
const canvas = ref<HTMLCanvasElement | null>(null);
const opposite: Record<Direction, Direction> = {
  right: "left",
  left: "right",
  up: "down",
  down: "up",
};
const x = ref(0);
const y = ref(0);
const direction = ref<Direction>("right");

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
  const handleKey = (e: KeyboardEvent) => {
    const keyMap: Record<string, Direction> = {
    ArrowUp: "up",
    ArrowDown: "down",
    ArrowLeft: "left",
    ArrowRight: "right"
  };
  const newDirection = keyMap[e.key];
  if (newDirection && newDirection !== opposite[direction.value]) {
    direction.value = newDirection;
  }
  };
  window.addEventListener("keydown", handleKey);

  onBeforeUnmount(() => {
    window.removeEventListener("keydown", handleKey);
  });
  setInterval(() => {
    const head = snake.value[snake.value.length - 1];
    let newHead = { x: head.x, y: head.y };
    if (direction.value === "right") newHead.x += 1;
    if (direction.value === "left") newHead.x -= 1;
    if (direction.value === "up") newHead.y -= 1;
    if (direction.value === "down") newHead.y += 1;
    snake.value.push(newHead);
    snake.value.shift();

    ctx.clearRect(0, 0, canvas.value!.width, canvas.value!.height);
    for (const segment of snake.value) {
      ctx.fillRect(segment.x * 20, segment.y * 20, 20, 20);
    }
  }, 1000);
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
