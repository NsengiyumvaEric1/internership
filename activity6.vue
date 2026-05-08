<template>
  <div class="game-container">
    <div class="score">Score: {{ score }}</div>
    
    <div v-if="gameOver" class="game-over">
      <h1>GAME OVER</h1>
      <button @click="resetGame">Restart</button>
    </div>

    <!-- Player Character -->
    <div 
      class="player" 
      :class="{ 'is-jumping': isJumping }"
    ></div>

    <!-- Obstacle -->
    <div 
      class="obstacle" 
      :style="{ left: obstaclePosition + 'px' }"
    ></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      score: 0,
      gameOver: false,
      obstaclePosition: 500,
      isJumping: false,
      animationId: null,
      playerY: 0, // Track vertical pos for collision
    };
  },
  methods: {
    jump() {
      if (this.isJumping) return;
      
      this.isJumping = true;
      // Reset jumping state after animation (0.5s)
      setTimeout(() => {
        this.isJumping = false;
      }, 500);
    },
    gameLoop() {
      if (this.gameOver) return;

      // Move obstacle left
      this.obstaclePosition -= 5;

      // Reset obstacle and increment score
      if (this.obstaclePosition < -50) {
        this.obstaclePosition = 600;
        this.incrementScore();
      }

      this.checkCollision();

      this.animationId = requestAnimationFrame(this.gameLoop);
    },
    checkCollision() {
      // Simple collision logic
      // Player is at left: 50px, width: 50px
      // Obstacle is width: 40px
      const playerPassed = this.obstaclePosition < 90 && this.obstaclePosition > 50;
      
      if (playerPassed && !this.isJumping) {
        this.gameOver = true;
        cancelAnimationFrame(this.animationId);
      }
    },
    incrementScore() {
      this.score += 1;
    },
    resetGame() {
      this.score = 0;
      this.gameOver = false;
      this.obstaclePosition = 500;
      this.gameLoop();
    }
  },
  mounted() {
    this.gameLoop();
    window.addEventListener('keydown', (e) => {
      if (e.code === 'Space') {
        this.jump();
      }
    });
  }
};
</script>

<style scoped>
.game-container {
  width: 600px;
  height: 200px;
  border-bottom: 2px solid #333;
  margin: 50px auto;
  position: relative;
  overflow: hidden;
  background-color: #f0f0f0;
}

.player {
  width: 50px;
  height: 50px;
  background-color: #3498db;
  position: absolute;
  bottom: 0;
  left: 50px;
}

.is-jumping {
  animation: jump-animation 0.5s linear;
}

.obstacle {
  width: 40px;
  height: 40px;
  background-color: #e74c3c;
  position: absolute;
  bottom: 0;
  
}

.score {
  position: absolute;
  top: 10px;
  right: 20px;
  font-family: Arial, sans-serif;
  font-weight: bold;
}

.game-over {
  text-align: center;
  margin-top: 50px;
}

@keyframes jump-animation {
  0% { bottom: 0; }
  30% { bottom: 100px; }
  70% { bottom: 100px; }
  100% { bottom: 0; }
}
</style>