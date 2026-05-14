<script lang="ts">
  import { onMount, onDestroy } from 'svelte';

  const GRID_SIZE = 20;
  const INITIAL_SPEED = 150;
  const MIN_SPEED = 60;
  const SPEED_INCREMENT = 2;

  type Point = { x: number; y: number };
  type Direction = 'UP' | 'DOWN' | 'LEFT' | 'RIGHT';

  let snake: Point[] = [{ x: 10, y: 10 }];
  let food: Point = { x: 5, y: 5 };
  let direction: Direction = 'RIGHT';
  let nextDirection: Direction = 'RIGHT';
  let gameOver = false;
  let score = 0;
  let highScore = 0;
  let gameInterval: number;
  let speed = INITIAL_SPEED;

  function getRandomPoint(): Point {
    return {
      x: Math.floor(Math.random() * GRID_SIZE),
      y: Math.floor(Math.random() * GRID_SIZE)
    };
  }

  function resetGame() {
    snake = [{ x: 10, y: 10 }];
    food = getRandomPoint();
    direction = 'RIGHT';
    nextDirection = 'RIGHT';
    gameOver = false;
    score = 0;
    speed = INITIAL_SPEED;
    startGame();
  }

  function startGame() {
    clearInterval(gameInterval);
    gameInterval = window.setInterval(move, speed);
  }

  function move() {
    if (gameOver) return;

    direction = nextDirection;
    const head = { ...snake[0] };

    switch (direction) {
      case 'UP': head.y -= 1; break;
      case 'DOWN': head.y += 1; break;
      case 'LEFT': head.x -= 1; break;
      case 'RIGHT': head.x += 1; break;
    }

    // Check collisions with walls
    if (head.x < 0 || head.x >= GRID_SIZE || head.y < 0 || head.y >= GRID_SIZE) {
      endGame();
      return;
    }

    // Check collisions with self
    if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
      endGame();
      return;
    }

    snake = [head, ...snake];

    // Check food
    if (head.x === food.x && head.y === food.y) {
      score += 10;
      if (score > highScore) highScore = score;
      food = getRandomPoint();
      
      // Increase speed
      if (speed > MIN_SPEED) {
        speed -= SPEED_INCREMENT;
        startGame();
      }
    } else {
      snake.pop();
    }
  }

  function endGame() {
    gameOver = true;
    clearInterval(gameInterval);
  }

  function handleKeydown(event: KeyboardEvent) {
    const keys = ['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', 'w', 'W', 's', 'S', 'a', 'A', 'd', 'D'];
    if (keys.includes(event.key)) {
      event.preventDefault();
    }

    switch (event.key) {
      case 'ArrowUp':
      case 'w':
      case 'W':
        if (direction !== 'DOWN') nextDirection = 'UP';
        break;
      case 'ArrowDown':
      case 's':
      case 'S':
        if (direction !== 'UP') nextDirection = 'DOWN';
        break;
      case 'ArrowLeft':
      case 'a':
      case 'A':
        if (direction !== 'RIGHT') nextDirection = 'LEFT';
        break;
      case 'ArrowRight':
      case 'd':
      case 'D':
        if (direction !== 'LEFT') nextDirection = 'RIGHT';
        break;
    }
  }

  onMount(() => {
    const savedHighScore = localStorage.getItem('snake-high-score');
    if (savedHighScore) highScore = parseInt(savedHighScore);
    startGame();
  });

  onDestroy(() => {
    clearInterval(gameInterval);
  });

  $: if (highScore > 0) {
    localStorage.setItem('snake-high-score', highScore.toString());
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="flex flex-col items-center gap-6">
  <div class="flex justify-between w-full max-w-[400px] font-mono text-lg px-2">
    <div class="text-base-600 dark:text-base-400">Score: <span class="text-accent-500 font-bold">{score}</span></div>
    <div class="text-base-600 dark:text-base-400">High Score: <span class="text-base-800 dark:text-base-100 font-bold">{highScore}</span></div>
  </div>

  <div 
    class="relative bg-base-100 dark:bg-base-900 border-4 border-base-200 dark:border-base-800 rounded-2xl overflow-hidden shadow-2xl shadow-black/20"
    style="width: 400px; height: 400px; display: grid; grid-template-columns: repeat({GRID_SIZE}, 1fr); grid-template-rows: repeat({GRID_SIZE}, 1fr);"
  >
    <!-- Food -->
    <div 
      class="bg-accent-500 rounded-full scale-75 animate-pulse"
      style="grid-column: {food.x + 1}; grid-row: {food.y + 1};"
    ></div>

    <!-- Snake -->
    {#each snake as segment, i}
      <div 
        class="bg-base-800 dark:bg-base-200 {i === 0 ? 'z-10 rounded-sm scale-110' : 'rounded-[2px] opacity-80'}"
        style="grid-column: {segment.x + 1}; grid-row: {segment.y + 1};"
      >
        {#if i === 0}
          <div class="flex gap-1 justify-center mt-1">
            <div class="w-1 h-1 bg-accent-500 rounded-full"></div>
            <div class="w-1 h-1 bg-accent-500 rounded-full"></div>
          </div>
        {/if}
      </div>
    {/each}

    {#if gameOver}
      <div class="absolute inset-0 bg-base-950/80 backdrop-blur-sm flex flex-col items-center justify-center text-center p-6 z-20">
        <h2 class="text-4xl font-bold text-white mb-2">Game Over</h2>
        <p class="text-base-400 mb-6">Final Score: {score}</p>
        <button 
          on:click={resetGame}
          class="px-8 py-3 bg-accent-500 hover:bg-accent-400 text-white font-bold rounded-full transition-all transform hover:scale-105"
        >
          Try Again
        </button>
      </div>
    {/if}
  </div>

  <div class="text-xs text-base-500 text-center space-y-1">
    <p>Use Arrow Keys or WASD to move</p>
    <p>Don't hit the walls or yourself!</p>
  </div>
</div>
