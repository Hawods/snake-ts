<template>
	<div id="snake">
		<div v-for="body in bodies" :style="{top: `${body.y}px`, left: `${body.x}px`}">
		</div>
	</div>
</template>

<script lang="ts">
import { computed, defineComponent, getCurrentInstance, inject, onBeforeUnmount, onMounted, reactive, Ref, ref, watch } from 'vue'

// 上下左右四个方向，取正负值方便判断是否相反方向
enum Direction {
    Up = 1,
    Down = -1,
    Left = 2,
    Right = -2,
}

// 从键盘事件中获取方向
const getDirection = (ev: KeyboardEvent) => {
	switch(ev.key) {
		case 'Up':
		case 'ArrowUp':
			return Direction.Up;
		case 'Down':
		case 'ArrowDown':
			return Direction.Down;
		case 'Left':
		case 'ArrowLeft':
			return Direction.Left;
		case 'Right':
		case 'ArrowRight':
			return Direction.Right;
		default: 
			return null;
	}
}

// 计算蛇头的下一个位置
const nextPosition = ({x, y}: Position, direction: Direction|null) => {
	const nextPosition = {x, y};
	switch(direction) {
		case Direction.Up:
			nextPosition.y -= 10;
			break;
		case Direction.Down:
			nextPosition.y += 10;
			break;
		case Direction.Left:
			nextPosition.x -= 10;
			break;
		case Direction.Right:
			nextPosition.x += 10;
			break;
		default: 
	}

	return nextPosition;
}

const createDefaultBodies = () => {
	return [{ x: 0, y: 0 }];
}

export default defineComponent({
  name: 'Snake',
	emits: ['eat', 'dead'],
	setup() {
		const instance = getCurrentInstance();
		const foodPosition = inject('foodPosition', reactive({x: 0, y: 0}));
		const	level = inject('level', ref(1));

		const bodies = ref(createDefaultBodies());
		const direction: Ref<Direction | null> = ref(null);
		const isAlive = ref(true);

		const reset = () => {
			bodies.value = createDefaultBodies();
			direction.value = null;
			isAlive.value = true;
		}

		// 坐标是否在身体上
		const isOnBody = ({x, y}: Position) => {
			for(const body of bodies.value) {
				if(body.x === x && body.y === y) {
					return true;
				}
			}

			return false;
		}

		// 移动
		const move = () => {
			if(!isAlive.value || !direction.value) {
				return;
			}

			// 计算偏移量
			let {x, y}  = nextPosition(bodies.value[0], direction.value);

			// 判断是否撞身体或墙
			if(isOnBody({x, y}) || x < 0 || x > 290 || y < 0 || y > 290) {
				isAlive.value = false;
				instance?.emit('dead');
				return;
			}

			// 头部向前增加一个单位
			bodies.value.unshift({x, y});
			// 判断是否吃到食物，吃到了抛出事件，没吃到尾部移除一个单位
			if(foodPosition.x === x && foodPosition.y === y) {
				instance?.emit('eat');
			} else {
				bodies.value.pop();
			}
		};

		// 控制移动方向
		const changeDirection = (ev: KeyboardEvent) => {
			const toDirection = getDirection(ev);
			if(direction.value === toDirection || toDirection === null) {
				return;
			}

			// 身长大于1时，不允许直接掉头
			if(bodies.value.length > 1) {
				const [head, body] = bodies.value;
				const headNext = nextPosition(head, toDirection)
				if(headNext.x === body.x && headNext.y === body.y) {
					return;
				}
			}

			direction.value = toDirection;
		};
		onMounted(() => {
			document.addEventListener('keydown', changeDirection);
		});
		onBeforeUnmount(() => {
			document.removeEventListener('keydown', changeDirection);
		});

		// 持续移动
		let interval = computed(() => 300 - (level.value - 1) * 30);
		let moveHandle: number;
		watch(level, () => {
			clearInterval(moveHandle);
			moveHandle = setInterval(move, interval.value);
		});
		onMounted(() => {
			moveHandle = setInterval(move, interval.value);
		});
		onBeforeUnmount(() => {
			clearInterval(moveHandle);
		});

		return {
			bodies,
			reset,
			isOnBody,
		}
	},
})
</script>

<style lang="less" scoped>
	#snake {
		& > div {
			position: absolute;
			width: 10px;
			height: 10px;
			background-color: #000;
			border: 1px solid #90ee90;
		}
	}
</style>