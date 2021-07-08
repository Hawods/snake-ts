<template>
	<div id="game-container">
		<div id="box">
			<Food ref="food" />
			<Snake ref="snake" @eat="eatFood" @dead="gameOver"/>
			<div v-if="isGameOver" id="game-over">
				<span>GAME OVER</span>
				<button @click="restart">重新开始</button>
			</div>
		</div>
		<ScorePanel ref="scorePanel" />
	</div>
</template>

<script lang="ts">
import { defineComponent, provide, reactive, ref } from 'vue'
import Food from './Food.vue'
import Snake from './Snake.vue'
import ScorePanel from './ScorePanel.vue'

export default defineComponent({
  name: 'GameContainer',
  components: {
		Food,
		Snake,
    ScorePanel
  },
	setup() {
		const isGameOver = ref(false);
		const level = ref(1);
		const foodPosition = reactive({ x: 0, y: 0 });

		provide('isGameOver', isGameOver);
		provide('level', level);
		provide('foodPosition', foodPosition);

		return {
			isGameOver,
			level,
			foodPosition,
		};
	},
	methods: {
		eatFood() {
			const scorePanel: any = this.$refs.scorePanel;
			scorePanel.addScore();
			const snake: any = this.$refs.snake;
			const food: any = this.$refs.food;
			do {
				food.changePosition();
			} while (snake.isOnBody(this.foodPosition));
		},
		gameOver() {
			this.isGameOver = true;
		},
		restart() {
			const snake: any = this.$refs.snake;
			snake.reset();
			const scorePanel: any = this.$refs.scorePanel;
			scorePanel.reset();
			const food: any = this.$refs.food;
			food.changePosition()
			this.isGameOver = false;
		}
	}
})
</script>

<style lang="less" scoped>
	#game-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-around;
		width: 360px;
		height: 420px;
		margin: 0 auto;
		background-color: #90ee90;
		border: 10px solid #000;
		border-radius: 20px;

		#box {
			position: relative;
			width: 302px;
			height: 302px;
			border: 1px solid #000;
			
			#game-over {
				position: absolute;
				top: 50%;
				left: 50%;
				width: 100px;
				height: 40px;
				text-align: center;
				margin-top: -20px;
				margin-left: -50px;
			}
		}
	}
</style>