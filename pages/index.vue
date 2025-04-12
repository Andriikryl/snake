<script setup lang="ts">
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
type Direction = "right" | "left" | "up" | "down";
type GameStatus = "playing" | "pause" | "lost";

const status = ref<GameStatus>("pause");
const canvas = ref<HTMLCanvasElement | null>(null);
const direction = ref<Direction>("right");
const score = ref(0);
const food = ref({ x: 10, y: 10 });

let canvasWidth = 400;
let canvasHeight = 400;

const snake = ref([
  { x: 5, y: 5 },
  { x: 6, y: 5 },
  { x: 7, y: 5 },
]);

const opposite: Record<Direction, Direction> = {
  right: "left",
  left: "right",
  up: "down",
  down: "up",
};

function generateFood(width: number, height: number) {
  food.value = {
    x: Math.floor(Math.random() * (width / 20)),
    y: Math.floor(Math.random() * (height / 20)),
  };
}

onMounted(() => {
  if (!canvas.value) return;

  const ctx = canvas.value.getContext("2d");
  if (!ctx) return;

  canvasWidth = canvas.value.width;
  canvasHeight = canvas.value.height;

  generateFood(canvasWidth, canvasHeight);

  const handleKey = (e: KeyboardEvent) => {
    const keyMap: Record<string, Direction> = {
      ArrowUp: "up",
      ArrowDown: "down",
      ArrowLeft: "left",
      ArrowRight: "right",
    };

    const newDirection = keyMap[e.key];
    if (e.key === " ") {
      status.value = status.value === "playing" ? "pause" : "playing";
    }

    if (newDirection && newDirection !== opposite[direction.value]) {
      direction.value = newDirection;
    }
  };

  window.addEventListener("keydown", handleKey);

  onBeforeUnmount(() => {
    window.removeEventListener("keydown", handleKey);
  });

  setInterval(() => {
    if (status.value !== "playing") return;

    const widthInBlocks = canvasWidth / 20;
    const heightInBlocks = canvasHeight / 20;

    const head = snake.value[snake.value.length - 1];
    let newHead = { x: head.x, y: head.y };

    if (direction.value === "right") newHead.x += 1;
    if (direction.value === "left") newHead.x -= 1;
    if (direction.value === "up") newHead.y -= 1;
    if (direction.value === "down") newHead.y += 1;

    const hitWall =
      newHead.x < 0 ||
      newHead.y < 0 ||
      newHead.x >= widthInBlocks ||
      newHead.y >= heightInBlocks;

    const hitSelf = snake.value.some(
      (segment) => segment.x === newHead.x && segment.y === newHead.y
    );

    if (hitWall || hitSelf) {
      alert("Game Over!");
      status.value = "lost";
      return;
    }

    snake.value.push(newHead);

    const ateFood = newHead.x === food.value.x && newHead.y === food.value.y;
    if (!ateFood) {
      snake.value.shift();
    } else {
      score.value++;
      generateFood(canvasWidth, canvasHeight);
    }

    ctx.clearRect(0, 0, canvasWidth, canvasHeight);

    ctx.fillStyle = "red";
    ctx.fillRect(food.value.x * 20, food.value.y * 20, 20, 20);

    ctx.fillStyle = "green";
    for (const segment of snake.value) {
      ctx.fillRect(segment.x * 20, segment.y * 20, 20, 20);
    }
  }, 200);
});

function startGame() {
  snake.value = [
    { x: 5, y: 5 },
    { x: 6, y: 5 },
    { x: 7, y: 5 },
  ];
  direction.value = "right";
  score.value = 0;
  status.value = "playing";
  generateFood(canvasWidth, canvasHeight);
}
</script>

<template>
  <main class="max-w-[500px] flex justify-center items-center">
    <Card>
      <CardHeader>
        <CardTitle>hello its snake game</CardTitle>
        <CardDescription>
          <p>score: {{ score }}</p>
        </CardDescription>
      </CardHeader>
      <CardContent>
        <section>
          <canvas
            ref="canvas"
            width="400"
            height="400"
            class="w-[400px] h-[400px] border"
          />
        </section>
      </CardContent>
      <CardFooter>
        <Button @click="startGame">Start Game</Button>
      </CardFooter>
    </Card>
  </main>
</template>
